# 微信小程序 UI 设计调研

- 调研日期：2026-06-22
- 调研范围：微信小程序在视觉设计、导航设计、布局适配、深色模式、无障碍、表单交互、原生组件限制方面的 UI 设计注意事项
- 结论摘要：微信小程序 UI 的关键不是做成独立 App，而是在微信容器内保持原生感、稳定性和可预期性。很多 UI 质量问题并非单纯审美问题，而是导航区、安全区、深色模式、原生组件层级、输入体验和无障碍没有提前纳入设计系统。

## 1. 设计基线

微信小程序首先是运行在微信容器里的产品，不是一个完全自由的移动端画布。设计时需要优先尊重微信已有的导航习惯、触达反馈、表单模式、系统栏占位和主题切换行为。过度强调“品牌独特性”而忽略宿主环境，通常会导致页面虽然好看，但使用起来不像微信里的产品。

WeUI 官方站和腾讯官方仓库都把 WeUI 定义为一套贴近微信原生视觉体验的样式体系，目标是让用户在微信内网页和微信小程序里的感知更加统一。因此更合理的设计策略通常是：

- 先以微信原生体验作为基础层
- 再在颜色、插画、图标细节、内容语气上做克制的品牌化
- 避免把核心导航、主按钮、表单反馈全部做成与微信心智相冲突的样式

## 2. 顶部导航设计

### 2.1 不要把导航栏当成纯视觉区域

小程序的顶部区域同时受状态栏、右上角胶囊按钮、机型安全区、系统主题和页面栈行为影响。设计稿如果只画一个固定高度标题栏，落地时往往会出问题。

官方配置里 `navigationStyle` 支持：

- `default`：默认导航栏
- `custom`：自定义导航栏，只保留右上角胶囊按钮

这意味着一旦启用自定义导航栏，设计稿必须显式考虑胶囊按钮的避让，而不是假设标题区可以完全自由铺满。

### 2.2 自定义导航栏必须基于真实尺寸适配

官方提供：

- `wx.getWindowInfo()`：返回 `statusBarHeight`、`safeArea`、`screenTop` 等信息
- `wx.getMenuButtonBoundingClientRect()`：返回右上角胶囊按钮的 `top`、`bottom`、`left`、`right`、`width`、`height`

设计含义：

- 顶部留白不能写死
- 标题垂直居中不能只对一个固定高度算
- 左右操作区宽度需要考虑胶囊按钮的真实位置
- 刘海屏、挖孔屏和不同系统下的头部布局必须容错

设计建议：

- 自定义头部至少定义 `状态栏区 + 导航内容区 + 与胶囊避让规则`
- 标题过长时优先做截断，不要硬挤压胶囊避让空间
- 左侧返回、标题、右侧操作三者应以胶囊区域为最高约束

## 3. 底部 tabBar 设计

官方支持自定义 `tabBar`，但它本质上是“平台导航能力的重绘”，不是普通组件换皮。

官方文档明确说明：

- 自定义 `tabBar` 从基础库 `2.5.0` 开始支持
- `tabBar` 相关配置项仍需完整声明
- 样式相关接口如 `wx.setTabBarItem` 在自定义模式下会失效
- 每个 tab 页下的自定义 `tabBar` 实例不同
- 选中态需要页面通过 `getTabBar()` 自己维护
- 推荐使用底部固定的 `cover-view + cover-image` 提高层级

设计含义：

- 只有在全局导航结构稳定时才值得自定义 `tabBar`
- 未读角标、选中态、按压态、图标切换态必须在设计稿中完整定义
- 底部安全区要算入 `tabBar` 总高度
- 不建议频繁变更 tab 数量、文案长度和视觉结构

## 4. 安全区与屏幕适配

### 4.1 安全区不是可选项

`wx.getWindowInfo()` 返回的 `safeArea` 用于描述竖屏正方向下的安全区域。官方同时说明，部分机型没有安全区概念，可能不会返回 `safeArea` 字段，开发者需要自行兼容。

这意味着设计稿不能只按“有安全区”设备思考，也不能只按单一机型出图。

设计建议：

- 顶部、底部、左右边缘都要有安全区策略
- 底部悬浮按钮、吸底操作条、底部弹层按钮区都要避开底部安全区
- 全屏海报、头图、视频封面可以视觉铺满，但可点击元素不要贴边

### 4.2 不要只按手机竖屏设计

官方配置和能力支持：

- `pageOrientation`
- `Page.onResize`
- `page-meta bindresize`

说明小程序并不只服务于单一竖屏手机场景，还要面对 iPad、横屏、尺寸变化等情况。

设计建议：

- 列表页至少思考宽屏下的留白和信息密度
- 详情页可预留双栏或更宽内容区方案
- 筛选面板、抽屉、弹层在大屏下不应简单等比放大

## 5. 页面背景、滚动和空白区设计

官方在 `page.json` 和 `page-meta` 中提供了多项背景相关配置：

- `backgroundColor`
- `backgroundColorTop`
- `backgroundColorBottom`
- `backgroundTextStyle`
- `root-background-color`
- `page-style`

这说明页面“非内容区域”的视觉一致性是正式设计内容，而不是实现细节。

设计建议：

- 下拉刷新状态的背景色与 loading 前景色要成对定义
- 页面底部未填满时出现的空白区，要与页面主背景一致
- 顶部回弹、iOS 顶底部区域颜色不要交给默认值碰运气

如果页面有沉浸式头图、滚动渐变导航、滚动变色标题栏，优先使用 `page-meta + navigation-bar` 的官方能力去设计，而不是完全依赖页面内自绘逻辑。

## 6. 深色模式

### 6.1 深色模式应进入设计系统

官方 `DarkMode 适配指南` 指出：

- 微信 iOS `7.0.12`、Android `7.0.13` 开始正式支持 DarkMode
- 小程序基础库 `v2.11.0` 起支持 `darkmode`
- `app.json` 开启 `darkmode: true`
- `themeLocation` 为 `darkmode: true` 时必填
- 基础组件、navigation bar 和 tab bar 会跟随主题变化
- 可通过系统信息获取当前 `theme`
- 可监听主题切换事件

设计含义：

- 浅色稿不是最终稿
- 深色模式不能靠简单反相
- 颜色体系应以语义 token 输出，而不是只给十六进制色板

设计建议：

- 至少定义 `light` 和 `dark` 两套语义色
- 主背景、次背景、边框、分割线、正文、次级文字、禁用态、错误态、成功态分别给出双主题值
- 图片、插画、空状态图标应考虑深色环境对比度
- 品牌主色在深色背景上的亮度和饱和度通常需要重新校正

## 7. 字体与可读性

`page-meta` 支持 `page-font-size` 和 `root-font-size`，并且可以设置为 `system`，表示跟随用户当前微信字体大小设置。

设计含义：

- 不能假设所有用户都用默认字号
- 字号放大后，紧凑型卡片、双行标题、横向按钮组最容易溢出

设计建议：

- 表单行高、按钮高度、卡片内边距不要压得过死
- 文字组件要允许两行或三行截断，而不是默认单行
- 数据卡片、价格、标签、时间等组合区要做放大字号回归验证

## 8. 无障碍设计

官方 `aria-component` 明确说明，小程序为了满足视障人士访问需求，自基础库 `2.7.1` 起支持部分 ARIA 标签。

重点能力：

- `aria-role`
- `aria-label`
- 以及一系列扩展 ARIA 属性

官方示例直接展示了用 `view` 模拟按钮时，补充：

- `aria-role="button"`
- `aria-label="提交表单"`

设计含义：

- 自绘按钮、可点击卡片、伪开关、图像入口、图标按钮都需要可读名称
- “只有图标、没有文字”的操作必须定义读屏语义
- 视觉上看得懂，不等于辅助功能可用

设计建议：

- 为每个非原生交互控件定义角色和朗读文案
- 图标按钮补充明确动作名，如“搜索”“关闭”“删除地址”
- 错误态、成功态、开关状态、折叠展开状态都应有语义输出

## 9. 表单与输入体验

### 9.1 尽量顺着原生输入模型设计

官方表单体系包括：

- `form`
- `button form-type`
- `input`
- `label`

官方 `input` 支持：

- `placeholder-style`
- `confirm-type`
- `cursor-spacing`
- 键盘高度变化相关能力

官方 `button` 支持：

- `form-type="submit"`
- `form-type="reset"`
- `open-type`
- `hover-class`

设计含义：

- 搜索框、登录框、验证码、地址、备注等输入场景应考虑键盘右下角动作
- 提交态、禁用态、校验态和加载态需要完整定义
- 占位文案只能辅助说明，不能替代真实标签体系

设计建议：

- 搜索场景优先定义 `confirm-type="search"` 的交互路径
- 多输入项流程要定义“下一个”“完成”“发送”等键盘动作
- 输入获得焦点后，底部按钮和键盘之间的距离要纳入设计
- 错误文案、字段说明、必填提示不要只靠颜色区分

## 10. 点击反馈与可操作性

官方 `button` 和 `navigator` 都支持 `hover-class`，即按压态反馈。微信平台显然默认用户应在点击后获得明确反馈。

设计建议：

- 不要为了“高级感”把所有点击反馈去掉
- 文本链接、卡片入口、工具按钮、底部操作按钮都应有按压态
- 按压态可以克制，但不应缺失

常见做法：

- 背景轻微变深
- 阴影收敛
- 透明度微调
- 图标与文本共同变化

## 11. 原生组件限制与 UI 风险

官方 `native-component` 说明以下组件涉及原生渲染特性：

- `camera`
- `canvas`
- `input`（仅 focus 时表现为原生组件）
- `live-player`
- `live-pusher`
- `map`
- `textarea`
- `video`

官方同时指出：

- 大部分原生组件已支持同层渲染
- 但组件内部仍由原生渲染，样式一般对内部无效
- 开发者工具不能完全还原真机表现
- 使用到原生组件时应尽量真机调试

设计含义：

- 地图、视频、相机区域上叠加复杂浮层时，要先确认真机表现
- 对原生组件内部做精细裁切、圆角遮罩、复杂滤镜、固定定位覆盖时风险较高
- 输入框 focus 前后样式和层级表现可能变化

设计建议：

- 原生组件区域优先采用简单、稳定的覆盖方案
- 真机验收要覆盖 iOS 和 Android
- 对地图、视频等区域上的关闭按钮、说明层、标签层保持克制

## 12. 推荐的设计产出物

如果要把这套调研真正落到项目里，建议至少输出以下内容：

### 12.1 设计 tokens

- 颜色：light/dark 双主题
- 字体：字号、行高、字重、极限场景规则
- 间距：页面边距、卡片内边距、区块间距
- 圆角：卡片、按钮、弹层、标签
- 阴影：浮层、悬浮按钮、弹窗

### 12.2 平台约束规范

- 自定义导航栏高度和避让规则
- 胶囊按钮安全边界
- 底部安全区适配
- tabBar 高度和图标区规范
- 原生组件覆盖规则

### 12.3 组件规范

- 按钮：默认、按压、禁用、加载
- 输入框：默认、聚焦、错误、禁用
- 卡片：信息层级、点击态、选中态
- 弹层：底部弹层、居中弹窗、半屏层
- 列表：单行、双行、富信息行

### 12.4 场景规范

- 列表页
- 详情页
- 搜索页
- 表单页
- 登录授权页
- 空状态页
- 错误页

## 13. 高频误区

- 把顶部导航栏高度写死，导致不同机型错位
- 自定义导航栏只出视觉稿，不定义胶囊避让逻辑
- 自定义 tabBar 只设计静态样式，不设计选中态、未读态和安全区
- 只做浅色模式，不做深色模式 token
- 只按默认字号出稿，不考虑微信系统字号放大
- 把 `view` 伪装成按钮或卡片入口，却不补无障碍语义
- 在地图、视频、相机等原生组件上叠大量复杂浮层，却不做真机验证
- 表单只画输入框外观，不定义键盘动作、错误态、提交态和加载态
- 为了视觉极简移除全部点击反馈

## 14. 适用于项目的落地原则

结合当前项目是微信生态相关前端工程，建议后续所有页面设计和实现遵循以下原则：

1. 先定义平台约束，再出高保真视觉稿。
2. 所有全局导航、页面头部、底部操作区都必须有安全区规则。
3. 所有颜色方案默认同时考虑浅色和深色主题。
4. 所有交互控件默认需要按压态、禁用态、加载态。
5. 所有自绘交互控件默认需要无障碍语义。
6. 所有表单页面默认考虑键盘抬起、焦点迁移、错误提示和提交反馈。
7. 任何涉及原生组件的复杂叠层视觉，必须经过真机验证后再定稿。

## 参考资料

1. [WeUI 官方站](https://weui.io/)
2. [Tencent/weui 官方仓库](https://github.com/Tencent/weui)
3. [小程序全局配置 app.json](https://developers.weixin.qq.com/miniprogram/dev/reference/configuration/app.html)
4. [页面配置 page.json](https://developers.weixin.qq.com/miniprogram/dev/reference/configuration/page.html)
5. [page-meta](https://developers.weixin.qq.com/miniprogram/dev/component/page-meta.html)
6. [navigation-bar](https://developers.weixin.qq.com/miniprogram/dev/component/navigation-bar.html)
7. [自定义 tabBar](https://developers.weixin.qq.com/miniprogram/dev/framework/ability/custom-tabbar.html)
8. [wx.getWindowInfo](https://developers.weixin.qq.com/miniprogram/dev/api/base/system/wx.getWindowInfo.html)
9. [wx.getMenuButtonBoundingClientRect](https://developers.weixin.qq.com/miniprogram/dev/api/ui/menu/wx.getMenuButtonBoundingClientRect.html)
10. [DarkMode 适配指南](https://developers.weixin.qq.com/miniprogram/dev/framework/ability/darkmode.html)
11. [aria-component](https://developers.weixin.qq.com/miniprogram/dev/component/aria-component.html)
12. [button](https://developers.weixin.qq.com/miniprogram/dev/component/button.html)
13. [input](https://developers.weixin.qq.com/miniprogram/dev/component/input.html)
14. [form](https://developers.weixin.qq.com/miniprogram/dev/component/form.html)
15. [navigator](https://developers.weixin.qq.com/miniprogram/dev/component/navigator.html)
16. [响应显示区域变化](https://developers.weixin.qq.com/miniprogram/dev/framework/view/resizable.html)
17. [native-component](https://developers.weixin.qq.com/miniprogram/dev/component/native-component.html)
