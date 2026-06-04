# Version Display

## 适用场景

设置页、关于页、调试信息、用户反馈页面、版本号展示。

## 来源优先级

1. 微信小程序运行时：`uni.getAccountInfoSync().miniProgram.version`。
2. 构建注入环境变量，例如 `import.meta.env.VITE_APP_VERSION`。
3. `package.json` 版本或构建脚本写入的常量。
4. 开发环境 fallback：`dev`、`local` 或 `0.0.0-dev`。

## 实现规则

- 封装 `getAppVersion()` 或同等工具，避免每个页面重复判断。
- 微信运行时版本为空时使用 fallback，因为开发版、体验版或本地调试可能没有正式版本号。
- 设置页或关于页统一展示，格式如 `版本 1.2.3`。
- 不把敏感构建信息、commit token 或内部环境名暴露给普通用户。

## 示例

```ts
export function getAppVersion() {
  // #ifdef MP-WEIXIN
  const version = uni.getAccountInfoSync?.().miniProgram?.version
  if (version)
    return version
  // #endif

  return import.meta.env.VITE_APP_VERSION || '0.0.0-dev'
}
```

## 跨端降级

- H5 使用环境变量或 package 注入。
- 其他小程序平台先查对应平台 API；没有稳定 API 时回退构建版本。

## 常见错误

- 直接展示 `undefined` 或空字符串。
- 每个页面各自写一套版本逻辑。
- H5 构建没有注入版本。
- 把内部构建号直接暴露给用户。

## 自检项

- 微信端优先使用 `getAccountInfoSync`。
- H5/开发环境有 fallback。
- 版本展示集中在设置页或关于页。
- 空值不会出现在 UI 中。
