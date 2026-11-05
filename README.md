# frontend-agent-skills

面向前端开发的 AI Agent Skills 集合，可安装到 Cursor / Codex / Trae / VS Code 等编辑器，约束 AI 按约定方式改代码。

## 目录结构

```
frontend-agent-skills/
├── README.md
├── LICENSE
└── skills/
    ├── vue-project-skill-builder/
    │   ├── SKILL.md
    │   ├── agents/
    │   └── references/
    │
    └── electron-vue3-typescript-desktop/
        └── SKILL.md
```

每个 skill 以 `SKILL.md` 为入口；详细规则、模板放在 `references/`，可选示例放在 `examples/`。

## Skills 一览

| Skill | 说明 |
|-------|------|
| [vue-project-skill-builder](./skills/vue-project-skill-builder/) | 分析已有 Vue Web 项目，生成适配目标编辑器的中文项目级开发 skill |
| [electron-vue3-typescript-desktop](./skills/electron-vue3-typescript-desktop/) | Electron + Vue 3 + TypeScript 桌面应用开发约定（主进程 / preload / IPC / 打包路径等） |

## 使用方式

将 `skills/` 下所需 skill 目录复制（或软链）到当前编辑器的 skills 目录，例如：

| 编辑器 | 常见路径 |
|--------|----------|
| Cursor | `.cursor/skills/<skill-name>/` |
| Trae | `.trae/skills/<skill-name>/` |
| Codex | `.codex/skills/<skill-name>/` 或全局 skills 目录 |
| VS Code | `.github/skills/<skill-name>/` |

复制后，在对话中按 skill 的 `name` / `description` 触发，或直接 `@` / 引用对应 skill。

> `vue-project-skill-builder` 会先询问你当前使用的 AI 编辑器，再按对应目录输出生成结果。

## 约定

- 所有 skill 统一放在 `skills/` 下，根目录只保留仓库说明与许可
- 默认中文说明与中文产物（除非用户要求其他语言）
- Skill 入口保持精简；细节下沉到 `references/`
- 不把推测写成项目事实；不确定项显式标注

## License

[MIT](./LICENSE)
