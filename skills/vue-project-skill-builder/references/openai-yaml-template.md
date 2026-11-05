# openai.yaml 约束

所有内置编辑器选项都采用标准 skill 目录，因此 `agents/openai.yaml` 遵循：

```yaml
interface:
  display_name: "项目名称开发规范"
  short_description: "基于项目真实结构与规范生成的开发约束，优先复用现有组件与模式。"
  default_prompt: "Use $skill-name to develop pages in this project by following the analyzed repository conventions and reusing existing components first."
```

## 约束

- `display_name` 尽量体现项目名和用途
- `short_description` 用中文，25 到 64 字符
- `default_prompt` 必须显式提到 skill 名
- 不需要额外字段时，不要添加无关配置
- 文案应体现这是“当前项目”的开发规范，不要写成全局通用能力
