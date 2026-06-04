# Subpackages

## 适用场景

新增页面、法律页面迁移、活动/结果/详情页面拆包、`pages.config.ts`、`pages.json`、uni-helper 自动生成页面。

## 分包原则

- 高频首屏、登录必要页面和 tabBar 页面留主包。
- 低频页面、法律页面、结果页、长内容页、编辑页、活动页和重资源图表页优先进入子包。
- 用户协议和隐私政策默认放入 legal 子包。

## pages.config.ts 方案

使用 uni-helper 时，优先在 `pages.config.ts` 中维护：

```ts
export default defineUniPages({
  pages: [
    { path: 'pages/login/index' },
  ],
  subPackages: [
    {
      root: 'pages/subpackages/legal',
      pages: [
        { path: 'user-agreement' },
        { path: 'privacy-policy' },
      ],
    },
  ],
})
```

如果项目通过文件扫描自动收集页面，必须排除分包目录，避免子包页面又被收集进主包。例如在对应插件配置中排除 `**/subpackages/**`。

## pages.json 方案

不使用 `pages.config.ts` 时，在 `pages.json` 维护等价结构：

```json
{
  "pages": [
    { "path": "pages/login/index" }
  ],
  "subPackages": [
    {
      "root": "pages/subpackages/legal",
      "pages": [
        { "path": "user-agreement" },
        { "path": "privacy-policy" }
      ]
    }
  ]
}
```

## 路径规则

- `root` 指向子包根目录。
- 子包内 `pages[].path` 相对 `root`，不要重复写完整路径。
- 跳转路径使用完整运行路径，例如 `/pages/subpackages/legal/privacy-policy`。
- 分包页面之间跳转仍使用完整页面路径，避免相对路径歧义。

## 常见错误

- `subPackages` 写成 `subpackages`。
- `root` 和 `path` 重复拼出双层目录。
- 自动页面插件未排除分包目录。
- 登录协议链接仍指向旧主包路径。

## 自检项

- 分包页面未出现在主包 `pages` 中。
- legal 页面在子包中。
- `pages.config.ts` 或 `pages.json` 路径与真实文件一致。
- 构建后没有重复页面警告。
