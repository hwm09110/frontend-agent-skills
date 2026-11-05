# 扫描顺序

按固定顺序扫描 Vue Web 项目，减少无效读取。

## 第一层：确认项目类型

- `package.json`
- `vite.config.*` / `vue.config.*` / `nuxt.config.*`
- `src/main.*`
- `src/App.vue`

目标：判断这是不是一个已有的 Vue Web 项目。

## 第二层：确认页面骨架

- 路由配置
- `src/views/**` / `src/pages/**`
- 典型列表页、表单页、详情页

目标：判断页面目录组织方式、命名方式、常见页面模式。

## 第三层：确认组件体系

- `src/components/**`
- 业务模块内部组件目录
- 被多处引用的共享组件

目标：区分项目级共享组件与模块级业务组件。

## 第四层：确认请求与状态

- `src/api/**`
- `src/services/**`
- `src/request/**`
- `src/stores/**`
- `src/composables/**`

目标：确认请求封装、API 定义、状态管理和数据流边界。

## 第五层：确认样式与工程规范

- 全局样式入口
- 主题变量和 design tokens
- `tsconfig.*`
- `.eslintrc*`
- `.prettierrc*`
- `stylelint` 配置

目标：确认样式方案和基础工程规范。
