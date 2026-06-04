# uni-weapp

`uni-weapp` 是一个面向 uni-app + Vue 微信小程序项目的 Codex 插件 marketplace，提供项目开发 agent、跨项目业务规则技能，以及 uni-app、Vue、Pinia、UnoCSS、Vite、uni-helper 等常用参考技能。

## 目录

- [功能概览](#功能概览)
- [插件目录](#插件目录)
- [安装](#安装)
- [更新](#更新)
- [本地开发调试](#本地开发调试)
- [使用方式](#使用方式)
- [内置技能](#内置技能)
- [适用场景](#适用场景)
- [验证](#验证)

## 功能概览

- 提供 `uni_weapp` Codex agent，用于 uni-app + Vue 微信小程序开发、审查和自检。
- 提供 `uni-weapp` 业务规则技能，覆盖登录协议、法务页面、分包、分享、头像昵称设置、版本展示、ECharts/lime-echart 等常见小程序问题。
- 打包 uni-app、uni-helper、Vue 最佳实践、Pinia、UnoCSS、Vite 等可复用技能。
- 适合在现有微信小程序项目中做功能实现、代码审查、规范检查和发布前业务自检。

## 插件目录

```text
uni-weapp/
├── .codex-plugin/
│   └── plugin.json
├── .codex/
│   └── agents/
│       └── uni-weapp.toml
├── skills/
│   ├── pinia/
│   ├── uni-app/
│   ├── uni-helper/
│   ├── uni-weapp/
│   ├── unocss/
│   ├── vite/
│   └── vue-best-practices/
├── LICENSE
└── README.md
```

marketplace manifest 位于 `.agents/plugins/marketplace.json`。插件 manifest 位于 `.codex-plugin/plugin.json`，插件名为 `uni-weapp`，展示名为 `Uni Weapp`。

## 安装

通过 Git marketplace 添加本仓库：

```bash
codex plugin marketplace add JsonLee12138/uni-weapp --ref main
```

然后安装插件：

```bash
codex plugin add uni-weapp@uni-weapp
```

如果使用 HTTPS Git URL，也可以写成：

```bash
codex plugin marketplace add https://github.com/JsonLee12138/uni-weapp.git --ref main
codex plugin add uni-weapp@uni-weapp
```

安装完成后，建议新开一个 Codex thread 来验证插件技能和 agent 是否已加载。

如果执行 `codex plugin marketplace add` 时看到下面的错误：

```text
marketplace root does not contain a supported manifest
```

说明传入的是普通插件目录，或者仓库根目录缺少 `.agents/plugins/marketplace.json`。本仓库采用和 `JsonLee12138/vibeRig` 相同的 Git URL source 写法，marketplace manifest 位于：

```text
.agents/plugins/marketplace.json
```

其中插件来源字段应使用 `url`，不是 `path`：

```json
{
  "source": {
    "source": "url",
    "url": "https://github.com/JsonLee12138/uni-weapp.git",
    "ref": "main"
  }
}
```

## 更新

远程仓库更新后，刷新 marketplace 快照并重新安装插件：

```bash
codex plugin marketplace upgrade uni-weapp
codex plugin add uni-weapp@uni-weapp
```

如果重新添加 marketplace 时需要指定分支，继续使用 `--ref main`。

## 本地开发调试

本地开发时，如果已经把当前源码目录作为 marketplace root 暴露给 Codex，可以用本地路径添加：

```bash
codex plugin marketplace add /Users/jsonlee/Projects/uni-weapp
codex plugin add uni-weapp@uni-weapp
```

本地修改插件后，建议更新 cachebuster 再重新安装，避免 Codex 继续使用旧缓存。这个流程只用于开发调试，普通安装请使用上面的 Git marketplace 命令：

```bash
python3 /Users/jsonlee/.codex/skills/.system/plugin-creator/scripts/update_plugin_cachebuster.py /Users/jsonlee/Projects/uni-weapp
codex plugin add uni-weapp@uni-weapp
```

## 使用方式

在 Codex 中可以直接描述 uni-app + Vue 微信小程序任务，例如：

```text
用 uni-weapp 帮我检查登录页是否满足微信小程序审核要求。
```

也可以显式点名技能：

```text
$uni-weapp 检查这个页面的登录协议、隐私协议和头像昵称设置流程。
$uni-app 帮我确认 pages.json 和 manifest.json 配置是否合理。
$vue-best-practices 审查这个 Vue 组件是否符合 Composition API 写法。
```

常见工作流：

1. 先让 `uni-weapp` 判断当前任务属于业务规则、uni-app 配置、Vue 组件、状态管理、样式或构建问题。
2. 再按需调用 `uni-app`、`uni-helper`、`vue-best-practices`、`pinia`、`unocss`、`vite` 等技能。
3. 最后让 `uni-weapp` 输出微信小程序业务自检结果和需要人工确认的边界。

## 内置技能

| 技能 | 用途 |
| --- | --- |
| `uni-weapp` | 微信小程序业务规则、审核敏感点、登录协议、法务页面、分包、分享、头像昵称、版本展示和图表自检。 |
| `uni-app` | uni-app 框架、组件、API、配置、平台差异和条件编译参考。 |
| `uni-helper` | uni-helper 生态、Vite 插件顺序、自动路由、自动导入和项目辅助工具参考。 |
| `vue-best-practices` | Vue 3、Composition API、`<script setup>`、TypeScript、组件和响应式最佳实践。 |
| `pinia` | Pinia store、state、getter、action、组合式 store 和 Vue 状态管理模式。 |
| `unocss` | UnoCSS 配置、预设、工具类、shortcuts、icons 和转换器参考。 |
| `vite` | Vite 开发服务器、插件、构建、HMR 和生产优化参考。 |

## 适用场景

- uni-app + Vue 微信小程序功能实现。
- 登录页、授权页、个人资料页、设置页和法务页面审查。
- 小程序分享、分包、版本展示、ECharts/lime-echart 图表实现。
- Vue 组件、Pinia store、UnoCSS 样式和 Vite/uni-helper 配置检查。
- 发布前业务规则自检和代码审查。

## 验证

修改插件 manifest 或目录结构后，可以运行插件校验：

```bash
python3 /Users/jsonlee/.codex/skills/.system/plugin-creator/scripts/validate_plugin.py /path/to/uni-weapp
```

把 `/path/to/uni-weapp` 替换为实际插件源码目录。
