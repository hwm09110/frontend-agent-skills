# 分析中间产物清理规则

分析文件只服务于第二阶段生成，不是最终交付物。项目级 skill 生成成功后，默认删除它们。

## 何时清理

满足以下全部条件时执行清理：

1. 第一阶段已写出分析文件
2. 第二阶段已成功生成项目级 skill（目标目录下至少有 `SKILL.md` 与 `references/`）
3. 用户没有明确要求保留分析文件

## 默认删除清单

- `.codex/project-analysis/project-analysis.json`
- `.codex/project-analysis/project-analysis.md`

随后：

1. 若 `.codex/project-analysis/` 已空，删除该目录
2. 若 `.codex/` 已空，且不含 `project-skill/` 或其他用户内容，删除该目录

若用户指定了其他分析输出路径，删除该路径下对应的 `project-analysis.json` / `project-analysis.md`，并按同样规则清理空目录。

## 禁止删除

- 已生成的项目级 skill 目录（如 `.cursor/skills/<project-slug>-project-skill/`）
- `.codex/project-skill/` 下的产物
- 分析目录中与本次流程无关的其他文件
- 第二阶段失败或未完成时的任何分析文件

## 执行要求

- 用删除命令实际移除文件，不要只在回复里说“可以删”
- 清理完成后，在最终回复中列出已删除路径
- 若用户要求保留，跳过本阶段，并在回复中说明仍保留分析文件
