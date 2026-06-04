# Review Checklist

Use this checklist before handing off a uni-app + Vue mini-program implementation or review.

## 登录审核

- 登录页、登录弹窗、手机号快捷验证前置页没有 `微信` 字样、WeChat 官方 logo 或官方授权暗示。
- 登录按钮使用中性文案，例如 `手机号快捷登录`、`快速登录` 或 `登录`。
- 协议未勾选时无法继续登录。

## 协议导航

- `agreement-row` 或同等组件已复用。
- 主题色、勾选态、禁用态和链接路径可配置。
- 用户协议和隐私政策链接能打开真实页面。

## 法律页面

- 用户协议和隐私政策默认位于子包。
- 主体、联系方式、数据收集、权限、第三方共享、未成年人保护和更新通知字段齐全。
- 自动生成正文只作为模板，已标记法律复核边界。

## 分包路径

- `subPackages` 配置正确。
- `root` 与 `path` 没有重复路径。
- 自动页面收集已排除分包目录。
- 登录、设置、协议入口使用完整可跳转路径。

## 分享参数

- `onShareAppMessage` 或平台等价 API 已配置。
- 分享 title、path 参数、imageUrl 和落地页可用。
- 分享记录 API 不阻断用户分享。
- H5/其他端有降级方案。

## 头像昵称

- 个人中心头像/昵称区域可点击进入设置页。
- 微信端使用 `chooseAvatar` 和 `input type="nickname"`。
- H5/其他端有上传和输入降级。
- 保存后接口数据、页面展示和用户状态同步一致。

## 版本号

- 微信端优先 `uni.getAccountInfoSync().miniProgram.version`。
- H5/开发环境有构建变量或 package fallback。
- 空版本不会展示给用户。

## 图表渲染

- 使用 `lime-echart` 或项目既有图表适配层。
- 容器有稳定尺寸，初始化时机正确。
- loading、empty、error 状态齐全。
- 页面卸载时 destroy，尺寸变化时 resize。
- 主题和跨端降级已处理。

## 跨端降级

- 微信专属 API 均有 H5 或其他小程序 fallback。
- 平台条件编译不会导致引用不存在的 API。
- 不支持的能力有用户可理解的反馈。
