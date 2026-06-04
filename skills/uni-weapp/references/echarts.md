# ECharts

## 适用场景

uni-app 中的折线图、柱状图、饼图、雷达图、结果页图表、统计页图表，以及 `lime-echart` 或项目既有图表适配层。

## 默认方案

- 优先使用项目已有图表方案。
- 当前项目没有统一方案时，uni-app 小程序优先考虑 `lime-echart` / `uni_modules` 或已验证的 ECharts 小程序适配层。
- 不在业务页面直接散落 ECharts 初始化细节；封装为图表组件或 composable。

## 实现规则

- 容器必须有稳定宽高，避免初始化时 canvas 为 0。
- 数据未就绪时显示 loading 或 empty state，不初始化空配置后再强行更新。
- 组件挂载后再初始化图表；容器尺寸变化时 resize。
- 页面卸载或组件卸载时销毁实例，避免内存泄漏。
- 深色/浅色主题和业务主题色应由 props 或全局 theme 注入。
- 大图表或低频图表可以懒加载 ECharts，降低首包压力。

## 小程序注意点

- canvas 渲染受平台限制，初始化时机要晚于 DOM/节点查询完成。
- 如果适配层需要组件 id 或 canvas 节点，确保列表中 id 唯一。
- 分包页面中的图表依赖要确认被正确打包。

## H5 降级

- H5 可直接使用标准 ECharts DOM renderer 或项目统一 wrapper。
- 同一业务组件需要区分小程序 canvas 适配层和 H5 DOM 初始化。

## 常见错误

- 容器没高度，图表空白。
- 多次进入页面后未 destroy，内存和事件监听累积。
- 数据变化后未调用 setOption 或 resize。
- 主题切换后图表颜色不更新。

## 自检项

- 使用 `lime-echart` 或项目既有适配层。
- 容器尺寸稳定，初始化时机正确。
- loading、empty、error 状态齐全。
- 卸载时 destroy，尺寸变化时 resize。
- H5/小程序渲染差异已处理。
