# 编辑器目标映射

先询问用户当前使用的 AI 编辑器，再决定标准 skill 结构的默认路径。

## 必问问题

在开始分析项目前，先询问：

```md
你当前主要使用哪个 AI 编辑器？

- Cursor
- Trae
- Codex
- VS Code
- 其他
```

如果用户回答不明确，继续追问，直到能确定目标编辑器。

## 默认映射

### Cursor

- 产物类型：标准 skill 目录
- 默认目录：`.cursor/skills/<project-slug>-project-skill/`
- 默认文件形式：`SKILL.md + references/ + agents/openai.yaml`
- 说明：如果仓库里已经存在 `.cursor/skills/` 或其他约定目录，优先沿用现有结构

### Trae

- 产物类型：标准 skill 目录
- 默认目录：`.trae/skills/<project-slug>-project-skill/`
- 默认文件形式：`SKILL.md + references/ + agents/openai.yaml`
- 说明：如果仓库里已经存在 `.trae/skills/` 或其他约定目录，优先沿用现有结构

### Codex

- 产物类型：项目级 skill 目录
- 默认目录：`.codex/project-skill/<project-slug>-project-skill/`
- 默认文件形式：标准 `SKILL.md + references/ + agents/openai.yaml`

### VS Code

- 产物类型：项目级 skill 目录
- 默认目录：`.github/skills/<project-slug>-project-skill/`
- 默认文件形式：标准 `SKILL.md + references/ + agents/openai.yaml`

## 其他编辑器

如果用户选择“其他”：

- 先询问编辑器名称
- 再询问该编辑器项目内希望使用哪个 skill 目录
- 再询问默认目录或让用户提供目标路径

如果用户也不清楚，则优先生成当前仓库根目录下的 `.ai-skills/<project-slug>-project-skill/` 兼容版本，并明确说明这是保守回退方案。

## 优先级规则

目录选择优先级如下：

1. 用户明确指定的当前仓库内路径
2. 仓库里已存在的目标编辑器规则目录
3. 本文档定义的默认目录

除非用户明确要求，否则不要输出到全局目录。
