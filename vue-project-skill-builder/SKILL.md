---
name: vue-project-skill-builder
description: 先询问用户当前使用的 AI 编辑器，再分析已有 Vue Web 项目并生成适配该编辑器目录的中文项目开发 skill；生成成功后默认删除分析阶段中间产物。适用于把现有 Vue Web 仓库沉淀为仅供当前项目使用的专属开发规范场景。
disable-model-invocation: true
---

# Vue 项目分析与 Skill 生成器

这是一个三阶段 skill：先分析项目，再基于分析结果生成项目级 skill，最后清理中间产物。

除非用户明确要求使用其他语言，否则默认使用中文响应，并默认生成中文产物。

## 工作流

按顺序执行，不要跳过阶段：

```
Task Progress:
- [ ] 第零阶段：确认目标 AI 编辑器
- [ ] 第一阶段：分析项目，产出分析文件
- [ ] 第二阶段：生成项目级 skill
- [ ] 第三阶段：清理分析中间产物
```

## 第零阶段：确认 AI 编辑器

每次调用本 skill 时，先询问用户当前使用的 AI 编辑器，再继续后续流程。

优先给出这些选项：Cursor、Trae、Codex、VS Code、其他。如果用户选择"其他"，继续追问目标编辑器名称及其期望的项目内规则/skill 目录。

编辑器决定 skill 的输出位置和目录结构，映射见 [references/editor-targets.md](references/editor-targets.md)。

## 第一阶段：分析项目

产出两份分析文件：

- `.codex/project-analysis/project-analysis.json`
- `.codex/project-analysis/project-analysis.md`

如果用户指定了其他输出路径，优先使用用户路径。

必须做到：

- 先确认目标是 Vue Web 项目；如果不是，说明不适用并停止
- 按固定顺序扫描，只输出有证据的结论
- 使用固定 JSON schema，区分项目级共享组件和模块级业务组件
- 把无法确认的内容写成待确认项，而不是编造

详细规则见：

- [references/scan-order.md](references/scan-order.md) — 扫描顺序与范围
- [references/analysis-schema.md](references/analysis-schema.md) — JSON schema
- [references/analysis-output-template.md](references/analysis-output-template.md) — 输出模板
- [references/evidence-rules.md](references/evidence-rules.md) — 证据与不确定项

## 第二阶段：生成项目级 skill

基于第一阶段结果，在当前仓库内生成适配目标编辑器目录的项目级 skill。默认只服务当前仓库，不做全局安装。

默认输出位置：

| 编辑器 | 默认路径 |
|--------|----------|
| Cursor | `.cursor/skills/<project-slug>-project-skill` |
| Trae | `.trae/skills/<project-slug>-project-skill` |
| Codex | `.codex/project-skill/<project-slug>-project-skill` |
| VS Code | `.github/skills/<project-slug>-project-skill` |

生成目录至少包含：

```
<project-slug>-project-skill/
├── SKILL.md
├── agents/openai.yaml
└── references/
    ├── project-summary.md
    ├── component-map.md
    ├── api-conventions.md
    ├── page-patterns.md
    └── open-questions.md
```

必须做到：

- 以 `project-analysis.json` 为主，`project-analysis.md` 为辅，不重新大范围扫描仓库
- 只写确定内容；冲突项保守处理并写入 `open-questions.md`
- 写入项目真实情况，不是泛化的 Vue 最佳实践

详细规则见：

- [references/generated-skill-structure.md](references/generated-skill-structure.md) — 生成结构
- [references/skill-writing-rules.md](references/skill-writing-rules.md) — 写作规则
- [references/missing-info-policy.md](references/missing-info-policy.md) — 缺失项处理
- [references/openai-yaml-template.md](references/openai-yaml-template.md) — openai.yaml 模板

## 第三阶段：清理分析中间产物

第二阶段成功后，删除第一阶段产出的分析文件，避免仓库残留仅供生成流程使用的中间产物。

默认清理 `.codex/project-analysis/project-analysis.json` 与 `.codex/project-analysis/project-analysis.md`，并按规则清理空目录。若用户指定过其他分析输出路径，清理对应路径下的分析文件。

例外：用户明确要求保留分析文件，或第二阶段失败/未完成时，不要自动删除。

详细规则见 [references/cleanup-rules.md](references/cleanup-rules.md)。

## 硬性要求、自检与禁止事项

执行流程时，硬性要求、输出前自检和禁止事项见 [references/hard-requirements.md](references/hard-requirements.md)。

## 参考文件

按需读取：

- [references/editor-targets.md](references/editor-targets.md) — 编辑器目录映射
- [references/scan-order.md](references/scan-order.md) — 扫描顺序与范围
- [references/analysis-schema.md](references/analysis-schema.md) — 分析结果 JSON schema
- [references/analysis-output-template.md](references/analysis-output-template.md) — 分析输出模板
- [references/evidence-rules.md](references/evidence-rules.md) — 证据与不确定项规则
- [references/generated-skill-structure.md](references/generated-skill-structure.md) — 生成结构详情
- [references/skill-writing-rules.md](references/skill-writing-rules.md) — Skill 编写规则
- [references/missing-info-policy.md](references/missing-info-policy.md) — 缺失项处理
- [references/openai-yaml-template.md](references/openai-yaml-template.md) — openai.yaml 模板
- [references/cleanup-rules.md](references/cleanup-rules.md) — 清理规则
- [references/hard-requirements.md](references/hard-requirements.md) — 硬性要求、自检与禁止事项
