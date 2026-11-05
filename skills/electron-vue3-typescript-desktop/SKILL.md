---
name: electron-vue3-typescript-desktop
description: Guides building and maintaining Electron desktop apps with Vue 3, TypeScript, Vite, Pinia, SCSS themes, preload IPC, and tray/window lifecycle. Use when the user works on electron/main, preload, renderer integration, packaged paths, dark mode, or desktop-specific UX.
disable-model-invocation: true
---

# Electron + Vue3 + TypeScript 桌面应用开发

面向在 **Electron + Vue 3 + TypeScript + Vite** 栈下开发桌面应用的约定与踩坑清单。默认假设渲染进程为 Vue SPA，主进程用 Node/Electron API，通过 **preload + contextBridge** 暴露受限 API。

## 架构分层

| 层级 | 职责 | 典型文件 |
|------|------|-----------|
| Main | 窗口、托盘、菜单、`app` 生命周期、IPC 处理、路径解析 | `electron/main/index.ts`、`paths.ts`、`tray.ts` |
| Preload | 仅暴露白名单 API 到 `window`，禁止业务逻辑堆叠 | `electron/preload/*.ts` |
| Renderer | Vue3 UI、路由、Pinia、样式与主题 | `src/**` |

**安全默认**：`contextIsolation: true`、`nodeIntegration: false`；需要 Node 能力时经 preload 或 IPC 在 main 执行。

## 路径与打包

- **开发**：`loadURL` 指向 Vite dev server；`import.meta.url` / `__dirname` 与生产不同。
- **生产**：`loadFile` 指向 `dist/index.html`；静态资源 `base` 与 `file://` 协议一致。
- 集中维护 `getAppPath` / `userData` / `resources` 等封装（参考 `electron/main/paths.ts`），避免在 renderer 硬编码绝对路径。

## IPC 约定

1. **通道命名**：`domain:action`（如 `todo:export`），避免与内置事件冲突。
2. **类型**：preload 暴露的 API 与 main `ipcMain.handle` 入参/返回值在共享 `types` 或 `contracts` 中定义，renderer 只依赖接口类型。
3. **错误**：main 抛出的错误在 preload 层转成 `{ ok: false, message }`，避免把 stack 直接给 UI。

### IPC 示例

**preload**（`electron/preload/index.ts`）：

```typescript
const { contextBridge, ipcRenderer } = require('electron');

contextBridge.exposeInMainWorld('electronAPI', {
  exportData: (payload: ExportPayload): Promise<ExportResult> =>
    ipcRenderer.invoke('todo:export', payload),
  getAppPath: (): Promise<string> =>
    ipcRenderer.invoke('app:getPath'),
});

interface Window {
  electronAPI?: {
    exportData: (payload: ExportPayload) => Promise<ExportResult>;
    getAppPath: () => Promise<string>;
  };
}
```

**main**（`electron/main/index.ts`）：

```typescript
import { ipcMain } from 'electron';

ipcMain.handle('todo:export', async (_event, payload: ExportPayload) => {
  try {
    const result = await doExport(payload);
    return { ok: true, path: result.path };
  } catch (err) {
    // 不把 stack 暴露给 renderer
    return { ok: false, message: err.message };
  }
});
```

**renderer**（Vue 组件）：

```typescript
const exportTodo = async () => {
  const result = await window.electronAPI?.exportData({ id: todoId });
  if (result?.ok) {
    message.success('导出成功');
  } else {
    message.error(result?.message ?? '导出失败');
  }
};
```

## Vue3 渲染进程

- **入口**：`createApp` → `pinia` → `router` → `mount`；路由用 `createWebHashHistory` 可避免 `file://` 下 history 路由 404（若未配置自定义协议）。
- **状态**：Pinia + `watch` 持久化时注意 **首次进入页面即需应用主题**（见下文「主题」）。
- **列表动画**：`TransitionGroup` 在 **搜索/筛选频繁变化** 时易产生闪烁；可在「过滤态」关闭列表过渡，仅在稳定列表态启用。

## 主题（SCSS + CSS 变量）

### 变量（至少）

在全局 SCSS（如 `src/style.scss`）的 `:root` 定义：

- `--bg-color`、`--card-bg`、`--text-primary`、`--text-secondary`
- `--border-color`、`--hover-bg`、`--active-color`、`--danger-color`

深色覆盖使用 **`html[data-theme='dark']` 与 `html.dark` 双选择器**，便于调试与第三方脚本。

### 切换与持久化

- **偏好**：`light | dark | system`，写入 `localStorage`。
- **实际主题**：`system` 时用 `matchMedia('(prefers-color-scheme: dark)')`；监听 `change` 同步更新。
- **应用**：同时设置 `document.documentElement.dataset.theme` 与 `classList.toggle('dark', isDark)`。
- **关键**：在 **`main.ts` 挂载前** 调用一次 `useAppStore(pinia)`（或等价初始化），确保 **任意路由首屏刷新** 都会应用已存主题，避免「只在设置页打开过才生效」。

### 过渡

全局可对 `background-color`、`color`、`border-color`、`box-shadow` 做短过渡；避免对 `width/height` 全局过渡导致性能问题。

### 深色可读性

- 卡片内文字显式 `color: var(--text-primary)`，避免继承链在深色下对比不足。
- Tag/Pill 避免浅色硬编码背景；用 `color-mix(in srgb, accent 14%, transparent)` 等与 `--card-bg` 协调。

## 弹窗与动效

- 删除确认等模态：**遮罩淡入** + **面板 `opacity` + `scale(0.95→1)`**，关闭反向；时长约 0.2–0.3s。
- 与主题变量统一边框/背景/阴影（`--border-color`、`--card-bg`、`--shadow-md`）。

## TypeScript 与 ESLint（Vue SFC）

- 模板内事件处理若需 DOM 类型，避免在 `<script setup>` 中直接使用 `HTMLInputElement` / `File` 等触发 `no-undef`：用 **结构化类型** 或抽到 `.ts` 工具函数。
- `globalThis`、`matchMedia` 等注意 SSR/非浏览器环境守卫（Electron renderer 为浏览器环境，但类型仍可能严格）。

## 桌面端体验

- **托盘**：图标路径区分 dev/prod；点击显示/隐藏主窗口；退出前确认可选。
- **单实例**：`app.requestSingleInstanceLock()`，第二实例聚焦已有窗口。
- **关闭行为**：`window-all-closed` 在 macOS 与 Windows 行为差异；按需 `dock` / `tray` 最小化到托盘而非退出。

## 开发命令与验证

- 开发：`electron` + `vite` 并行（以项目 `package.json` scripts 为准）。
- 提交前：`typecheck`、`lint`、`build`（含 `electron-builder` 或等价打包脚本时单独跑一遍）。

## 与用户沟通

- 中文回复；提交信息遵循用户 Conventional Commits + 中文规则。
- 大改动前先对齐：是否 Hash 路由、是否系统托盘常驻、数据存 `userData` 还是仅 localStorage。

## 何时读取本 Skill

用户提到 **Electron、主进程、preload、托盘、打包路径、桌面窗口、file 协议、与 Vue 联调、深色主题在桌面端丢失** 等场景时，优先按本文件约定实现与排查。
