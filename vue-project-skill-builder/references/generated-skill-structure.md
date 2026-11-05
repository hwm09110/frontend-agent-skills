# 生成结果结构

项目级产物默认生成在当前仓库内，所有内置编辑器选项都生成标准 skill 结构，具体目录由目标编辑器决定。

编辑器映射参见：

- `references/editor-targets.md`

## 标准 skill 结构

所有内置编辑器选项统一使用下面的结构：

```text
<editor-specific-dir>/
└── <project-slug>-project-skill/
    ├── SKILL.md
    ├── agents/
    │   └── openai.yaml
    └── references/
        ├── project-summary.md
        ├── component-map.md
        ├── api-conventions.md
        ├── page-patterns.md
        └── open-questions.md
```

除非用户明确要求，否则不要输出到全局 `CODEX_HOME/skills`。

常见目录示例：

```text
.cursor/skills/<project-slug>-project-skill/
.trae/skills/<project-slug>-project-skill/
.codex/project-skill/<project-slug>-project-skill/
.github/skills/<project-slug>-project-skill/
```

如果仓库内已经存在对应编辑器的 skill 目录结构，优先沿用现有结构。

## 文件职责

- `SKILL.md`：核心执行规则，控制后续 AI 的工作流。
- `project-summary.md`：项目结构、目录、页面与工程规范概览。
- `component-map.md`：共享组件、模块组件、复用优先级和使用建议。
- `api-conventions.md`：请求库封装、API 定义、错误处理、类型组织。
- `page-patterns.md`：列表页、表单页、详情页的典型实现方式。
- `open-questions.md`：未确认项、冲突项、人工补充建议。

## 核心原则

- `SKILL.md` 保持短而强约束。
- 细节沉淀到 references。
- 所有内容都来自分析结果，不从通用最佳实践直接推导。
- 生成结果默认仅服务当前仓库，不作为全局通用 skill 安装。
- 先适配目标编辑器，再决定标准 skill 结构放在哪个目录。
- 项目级 skill 生成成功后，按 `cleanup-rules.md` 删除分析中间产物；最终仓库只保留项目级 skill，不默认保留 `.codex/project-analysis/`。
