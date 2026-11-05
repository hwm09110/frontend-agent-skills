---
name: vue-project-skill-builder
description: 先询问用户当前使用的 AI 编辑器，再分析已有 Vue Web 项目并生成适配该编辑器目录的中文项目开发 skill；生成成功后默认删除分析阶段中间产物。适用于把现有 Vue Web 仓库沉淀为仅供当前项目使用的专属开发规范场景。
---

# Vue 项目分析与 Skill 生成器

这是一个单入口、三阶段的 skill。必须先分析项目，再基于分析结果生成项目级 skill，最后清理分析阶段的中间产物。不要跳过分析阶段直接生成规范。

除非用户明确要求使用其他语言，否则默认使用中文响应，并默认生成中文产物。

## 第零阶段：先确认 AI 编辑器

每次调用本 skill 时，先询问用户当前使用的 AI 编辑器，再继续后续流程。不要跳过这一步。

优先给出这些常见选项：

- Cursor
- Trae
- Codex
- VS Code
- 其他

如果用户没有说明编辑器，先提问，再继续。
如果用户选择“其他”，继续追问目标编辑器名称，以及它期望的项目内规则/skill 目录。

编辑器决定两件事：

- 标准 skill 结构放到哪个项目内目录
- 生成后的目录命名和使用说明

编辑器映射参见：

- `references/editor-targets.md`

## 适用范围

- 只面向已有的 Vue Web 项目。
- 第一版不处理微信小程序、React、uni-app、Taro、Electron 主进程、后端服务项目。
- 如果发现目标仓库明显不是 Vue Web，明确说明不适用并停止，不要硬套模板。

## 双阶段工作流

### 第一阶段：分析项目

目标是产出两份分析文件：

- `.codex/project-analysis/project-analysis.json`
- `.codex/project-analysis/project-analysis.md`

如果用户指定了其他输出路径，优先使用用户路径。

这一阶段必须：

- 先确认目标是不是 Vue Web 项目
- 按固定顺序扫描项目
- 只输出有证据的结论
- 使用固定 JSON schema
- 明确区分项目级共享组件和模块级业务组件
- 把无法确认的内容写成待确认项，而不是编造

扫描顺序与 schema 参见：

- `references/scan-order.md`
- `references/analysis-schema.md`
- `references/analysis-output-template.md`
- `references/evidence-rules.md`

### 第二阶段：按编辑器生成项目级 skill

目标是基于第一阶段结果，在当前仓库内生成适配目标编辑器目录的项目级 skill。默认只服务当前仓库，不做全局安装。

默认按编辑器选择输出位置：

- Cursor：`.cursor/skills/<project-slug>-project-skill`
- Trae：`.trae/skills/<project-slug>-project-skill`
- Codex：`.codex/project-skill/<project-slug>-project-skill`
- VS Code：`.github/skills/<project-slug>-project-skill`

如果用户指定了当前仓库内的其他路径，优先使用用户路径。
除非用户明确要求，否则不要生成到全局目录。

生成的新 skill 目录至少包含：

- `SKILL.md`
- `agents/openai.yaml`
- `references/project-summary.md`
- `references/component-map.md`
- `references/api-conventions.md`
- `references/page-patterns.md`
- `references/open-questions.md`

这一阶段必须：

- 只读取第一阶段的分析结果，不重新大范围扫描仓库
- 以 `project-analysis.json` 为主，`project-analysis.md` 为辅
- 冲突项保守处理，并写入 `open-questions.md`
- 只把确定内容写成规则
- 让生成出的 skill 便于人工后续修改
- 让生成出的产物只服务当前仓库，不作为全局通用能力安装
- 让生成结果符合目标编辑器的项目内目录习惯

生成结构与缺失项处理规则参见：

- `references/editor-targets.md`
- `references/generated-skill-structure.md`
- `references/skill-writing-rules.md`
- `references/missing-info-policy.md`
- `references/openai-yaml-template.md`
- `references/cleanup-rules.md`

### 第三阶段：清理分析中间产物

目标是在项目级 skill 已成功生成后，删除第一阶段写出的分析文件，避免仓库残留仅供生成流程使用的中间产物。

默认清理范围：

- `.codex/project-analysis/project-analysis.json`
- `.codex/project-analysis/project-analysis.md`
- 若 `.codex/project-analysis/` 目录在删除后为空，一并删除该空目录
- 若 `.codex/` 目录在删除后为空，且其中没有 `project-skill/` 或其他保留内容，一并删除该空目录

如果用户在第一阶段指定了其他分析输出路径，清理时删除用户指定路径下的对应分析文件，而不是只删默认路径。

这一阶段必须：

- 仅在第二阶段项目级 skill 已成功写出后执行
- 只删除分析阶段产物，绝不删除已生成的项目级 skill 目录
- 清理前确认目标文件确实是本轮（或明确指定的）分析产物
- 清理完成后在回复中说明已删除哪些分析文件

例外（不要自动删除）：

- 用户明确要求保留分析文件
- 第二阶段生成失败或未完成
- 目标路径下还有与本次分析无关、用户另作他用的文件时，只删分析文件本身，不误删其他内容

清理规则参见：

- `references/cleanup-rules.md`

## 第一阶段的硬性要求

### 1. 先确认项目类型

优先读取并判断：

- `package.json`
- `vite.config.*` / `vue.config.*` / `nuxt.config.*`
- `src/main.*`
- `src/App.vue`
- 路由配置和页面目录

至少确认：

- Vue 版本
- 构建工具
- UI 框架或组件库
- 是否存在典型 Web 页面目录和组件目录

### 2. 固定扫描范围

必须按如下优先级建立上下文：

1. `package.json`
2. 构建配置
3. 路由配置
4. 页面目录
5. 共享组件目录
6. 模块级组件目录
7. request / api / service 目录
8. store / composables 目录
9. 全局样式、主题、tokens
10. lint / prettier / tsconfig / stylelint

不要一开始就遍历整个仓库。

### 3. 分析结果覆盖维度

分析结果必须覆盖：

- 项目基础信息：框架版本、构建工具、目录结构、路由组织方式
- 页面开发规范：页面目录、命名规则、SFC 组织顺序、常见页面类型
- 组件体系：项目级共享组件、模块级业务组件、组件放置规则、复用优先级
- 请求与 API 规范：请求库封装位置、拦截器、错误处理、API 定义方式、类型定义方式
- 状态与数据流：Pinia、Vuex、composables、局部状态边界
- 样式规范：SCSS、CSS Modules、工具类、主题变量、design tokens
- 页面模式模板：列表页、表单页、详情页
- 组件抽离规则：哪些场景会抽组件，哪些通常保留页面内
- 风险与不确定项：无法自动确认、需要人工补充的部分

### 4. 证据与不确定项

- 每个重要结论尽量附带证据路径
- 证据路径使用相对仓库根目录
- 不确定项用 `unknown`、空数组、或“未确认”表示
- 同时写入 `open_questions`

## 第二阶段的硬性要求

### 1. 只基于分析结果生成

生成项目级 skill 时：

- 以 JSON 为主输入源
- 以 Markdown 做语义补充
- 不要重新读大仓库去“补全”

如果没有结构化分析结果，提示用户先运行本 skill 的分析阶段。

### 2. 生成的是项目规范，不是通用套话

新 skill 必须写入项目真实情况，例如：

- 进入项目先看哪些目录和文件
- 页面通常放在哪里
- 优先复用哪些共享组件
- 模块级组件通常怎么放
- 请求库怎么封装、API 怎么定义、类型怎么组织
- 状态管理与 composables 的使用边界
- 样式与主题变量怎么处理
- 列表页、表单页、详情页有哪些典型模式
- 什么时候该抽组件，什么时候不要抽

优先写：

- 规则
- 证据
- 推荐做法

不要只输出泛化的 Vue 最佳实践。

### 2.1 当前项目限定规则

生成项目级产物时，默认假设它只供当前仓库使用，因此：

- 路径说明优先使用当前仓库相对路径
- references 内容围绕当前仓库真实目录组织
- 不要把它表述成“可适用于类似项目”的通用能力
- 不要默认放到全局 skills 目录
- 如果用户没有特别要求，不要生成任何“安装到全局”的说明

### 2.2 编辑器适配规则

根据用户选择的编辑器生成对应产物：

- Cursor：生成标准 skill 目录，默认放到 `.cursor/skills/`
- Trae：生成标准 skill 目录，默认放到 `.trae/skills/`
- Codex：生成项目级 skill 目录，默认放到 `.codex/project-skill/`
- VS Code：生成项目级 skill 目录，默认放到 `.github/skills/`

如果仓库里已经存在目标编辑器对应的规则目录或历史文件，优先与现有结构保持一致。
所有内置编辑器选项都生成标准 skill 结构，不生成单独规则文件。

### 3. 缺失项集中收敛

如果分析结果缺失关键字段：

- 不要自己编造
- 在 `SKILL.md` 中只写确定部分
- 在 `references/open-questions.md` 中列出待确认项、影响和建议补充方式

## 输出前自检

在完成整个流程前，至少检查：

- 是否真的先完成了分析，再开始生成 skill
- 分析结果是否覆盖了所有核心维度
- 是否给出了足够的证据路径
- 是否把不确定项明确标出
- 生成出的 skill 是否真的基于项目结果，而不是通用模板
- 目标编辑器是否已经先确认
- 生成格式和目录是否与目标编辑器匹配
- `SKILL.md` 是否足够短、足够明确
- references 是否覆盖了后续 AI 最需要反复查阅的信息
- 第二阶段成功后，是否已按规则清理分析中间产物
- 清理时是否误删了项目级 skill 或其他无关文件

## 禁止事项

- 不要在没确认编辑器之前就开始生成项目级产物
- 不要跳过分析阶段直接生成项目级 skill
- 不要把推测写成项目事实
- 不要把分析阶段的“推荐做法”冒充成项目现状
- 不要把所有内容都堆进生成后 skill 的 `SKILL.md`
- 不要生成与 skill 无关的 README、安装说明、变更日志
- 不要在没有证据时断言某个目录就是全项目规范入口
- 不要默认把生成出的项目级 skill 部署到全局 `CODEX_HOME/skills`
- 不要忽略目标编辑器的目录差异
- 不要在项目级 skill 生成失败时删除分析文件
- 不要把分析中间产物当作需要长期保留的仓库文档，除非用户明确要求保留

## 参考文件

按需读取这些参考文件：

- `references/editor-targets.md`
- `references/scan-order.md`
- `references/analysis-schema.md`
- `references/analysis-output-template.md`
- `references/evidence-rules.md`
- `references/generated-skill-structure.md`
- `references/skill-writing-rules.md`
- `references/missing-info-policy.md`
- `references/openai-yaml-template.md`
- `references/cleanup-rules.md`
