# 分析结果 Schema

`project-analysis.json` 使用固定结构，供下游项目级 skill 生成使用。

```json
{
  "meta": {
    "project_name": "",
    "repo_root": "",
    "generated_at": "",
    "scope": "vue-web",
    "summary": ""
  },
  "project_basics": {
    "framework": { "summary": "", "value": "", "confidence": "high", "evidence": [] },
    "build_tool": { "summary": "", "value": "", "confidence": "high", "evidence": [] },
    "ui_stack": [],
    "directory_patterns": [],
    "routing": { "summary": "", "confidence": "medium", "evidence": [] }
  },
  "page_conventions": {
    "page_dirs": [],
    "naming": { "summary": "", "confidence": "medium", "evidence": [] },
    "sfc_structure": { "summary": "", "confidence": "medium", "evidence": [] },
    "common_page_types": [],
    "dominant_patterns": []
  },
  "component_system": {
    "project_shared_components": [],
    "module_components": [],
    "placement_rules": { "summary": "", "confidence": "medium", "evidence": [] },
    "reuse_priority": []
  },
  "request_and_api": {
    "request_library": { "summary": "", "value": "", "confidence": "medium", "evidence": [] },
    "request_wrapper": { "summary": "", "confidence": "medium", "evidence": [] },
    "interceptors": { "summary": "", "confidence": "low", "evidence": [] },
    "error_handling": { "summary": "", "confidence": "low", "evidence": [] },
    "api_definition": { "summary": "", "confidence": "medium", "evidence": [] },
    "typing_strategy": { "summary": "", "confidence": "medium", "evidence": [] }
  },
  "state_and_data_flow": {
    "state_tools": [],
    "usage_boundary": { "summary": "", "confidence": "medium", "evidence": [] },
    "composables": { "summary": "", "confidence": "medium", "evidence": [] }
  },
  "styling": {
    "approach": [],
    "theme_tokens": { "summary": "", "confidence": "low", "evidence": [] },
    "global_style_entry": [],
    "style_rules": { "summary": "", "confidence": "medium", "evidence": [] }
  },
  "page_patterns": {
    "list_page": { "summary": "", "evidence": [] },
    "form_page": { "summary": "", "evidence": [] },
    "detail_page": { "summary": "", "evidence": [] }
  },
  "component_extraction": {
    "extract_when": [],
    "keep_inline_when": [],
    "summary": "",
    "evidence": []
  },
  "risks_and_questions": {
    "uncertain_items": [],
    "open_questions": [],
    "risks": []
  }
}
```

## 字段要求

- `summary`：一句到三句中文概括。
- `value`：适合单值结论的字段，没有就省略。
- `confidence`：`high`、`medium`、`low`。
- `evidence`：相对仓库根目录的路径数组。
- 数组字段优先放简短结论对象或路径，不要塞大段自然语言。

## 缺失项处理

- 不确定时，不要伪造值。
- 可以使用空数组、空字符串，或在 `summary` 中写“未确认”。
- 同时把该项写入 `risks_and_questions.open_questions`。
