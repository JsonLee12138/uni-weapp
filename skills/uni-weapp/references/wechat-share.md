# WeChat Share

## 适用场景

微信小程序分享、页面级分享按钮、分享标题、路径参数、分享图片、落地页、分享记录接口、H5/其他端降级。

## 实现规则

- 页面级分享使用 `onShareAppMessage`；需要朋友圈时使用 `onShareTimeline`。
- 分享参数至少明确：`title`、`path`、可选 `imageUrl`。
- `path` 必须包含落地页可恢复上下文所需参数，例如资源 id、邀请人 id、场景值或来源。
- 分享图片优先使用稳定 CDN 或小程序可访问资源，避免本地临时路径。
- 需要统计时，在分享前或分享成功后的可行时机调用分享记录 API；接口失败不应阻断分享。
- 入口按钮使用平台支持的 `open-type="share"` 或页面右上角分享能力。

## 示例形态

```ts
onShareAppMessage(() => ({
  title: shareTitle.value,
  path: `/pages/subpackages/result/detail?id=${id.value}&from=share`,
  imageUrl: shareImage.value,
}))
```

## 跨端降级

- H5 端使用 Web Share API、复制链接、生成海报或展示不可用提示。
- 非微信小程序端根据平台能力映射分享 API；没有能力时提供复制链接。
- 分享记录 API 应带平台字段，避免不同端数据混淆。

## 常见错误

- 分享 path 缺少业务 id，落地页无法恢复。
- 分享图片使用开发环境临时地址。
- 分享标题写死，无法按页面内容变化。
- H5 点击分享按钮无反馈。

## 自检项

- `onShareAppMessage` 或平台等价 API 已配置。
- title、path 参数、imageUrl 和落地页都可用。
- 分享记录 API 失败不影响用户操作。
- H5/其他端有降级方案。
