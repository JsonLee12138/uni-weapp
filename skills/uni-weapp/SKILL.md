---
name: uni-weapp
description: Cross-project business rules for uni-app + Vue WeChat mini-program work. Use with uni-app, uni-helper, vue-best-practices, pinia, unocss, and vite when implementing or reviewing login agreement rows, audit-safe login copy, legal pages, subpackages, WeChat sharing, profile avatar/nickname settings, version display, ECharts/lime-echart charts, or final mini-program business self-checks.
---

# uni-weapp

Use this skill after the project has been identified as a uni-app + Vue mini-program, or when the task asks for reusable WeChat mini-program business conventions. Keep general framework behavior in the framework skills; use this skill for product and review rules that have caused cross-project issues.

## Workflow

1. Identify the touched feature area.
2. Load the smallest matching reference file from `references/`.
3. Apply existing project patterns before adding new abstractions.
4. Keep WeChat-only APIs behind platform guards or provide H5/other-mini-program fallback.
5. Run the relevant checks from `references/review-checklist.md` before final delivery.

## Reference Routing

- Login, phone quick login, agreement checkbox, privacy/user agreement links, or login audit copy: read `references/login-and-agreement.md`.
- User agreement, privacy policy, legal page content fields, or legal page subpackage placement: read `references/legal-pages.md`.
- `pages.config.ts`, `pages.json`, `subPackages`, package boundaries, or generated page collection: read `references/subpackages.md`.
- `onShareAppMessage`, `onShareTimeline`, share title/path/image, share landing, or share record API: read `references/wechat-share.md`.
- Profile page avatar/nickname entry, `chooseAvatar`, `input type="nickname"`, settings page, save behavior, or user store sync: read `references/profile-settings.md`.
- Settings/about page version display or build/runtime version fallback: read `references/version-display.md`.
- ECharts, `lime-echart`, canvas sizing, lazy chart loading, destroy, theme, loading, or empty chart states: read `references/echarts.md`.
- Before handing off a mini-program feature or review: read `references/review-checklist.md`.

## Boundaries

- Do not generate final legal text without app owner, company/person subject, contact, data collection, permissions, third-party sharing, minor-protection, and update-notice inputs.
- Do not add "微信" copy, WeChat official logo, or official authorization implications to login pages, login dialogs, or phone quick-login preflight screens.
- Do not duplicate generic Vue, Pinia, UnoCSS, Vite, uni-app, or uni-helper guidance here.
- Prefer enhancing an existing page/component when the project already has a login page, legal page, settings page, share composable, version display, or chart wrapper.

## Final Output

When this skill is used, include the reference files consulted, the checks run, any cross-end fallback, and any remaining manual legal or platform review needed.
