---
name: claude-source-access
description: 如何拿到 Claude 桌面/网页的真实最新前端源码（调研 Claude 自身 UX/Cowork 逻辑时用，零截图）
metadata: 
  node_type: memory
  type: reference
  originSessionId: 5173866e-3a14-4bf1-9097-53bb687273e7
---

调研 Claude 自身（Cowork / chat 编排 UI）的"真实最新交互逻辑"时，最佳取证是**读 shipped 源码**，非截图：

1. **claude.ai web 前端**（对话内编排 UI：thinking/tool 卡/artifact/permission/ask-user 等）：
   - chunk 托管在 `https://assets-proxy.anthropic.com/claude-ai/v2/assets/v1/<hash>.js`，**普通 curl 直接 200、免登录、不触发 Cloudflare**（主站 claude.ai/ 才挡，403 "Just a moment"）。
   - 入口 `index-*.js`(~7.7MB, Vite, `__vite__mapDeps`) 列出全部懒加载 chunk；正则 `[A-Za-z0-9_-]+-[A-Za-z0-9_-]{8}\.js` 提取后批量 curl。
   - Cowork 专属组件名直接可见：CoworkPermissionModeSelect / CoworkToolApproval / CoworkArtifactToolUseCell / CoworkUnsupervisedModeWarningModal / CoworkSafetyBanner / CoworkStatusGlyph 等。

2. **桌面 app.asar**（桌面壳层+后端：VM/任务生命周期/权限门/memory sync/scheduled/plugins）：
   - `npx --yes @electron/asar extract "/Applications/Claude.app/Contents/Resources/app.asar" <out>`；主进程 `.vite/build/index.js`(~13MB) 含大量可读标识符（cowork_*, coworkCamelCase）。
   - 本地化文案：`/Applications/Claude.app/Contents/Resources/en-US.json`（仅原生壳层；对话内编排文案在 web）。
   - app 版本看 Info.plist CFBundleShortVersionString。

**走不通的路（别再试）**：桌面 Claude(Electron) **fuse 锁死**，带 `--remote-debugging-port` 启动即拒绝运行 → 无法 CDP 接入读 UI；本机 9222 现有 Chrome `/json/version` 404 不可复用。Playwright(py 1.58, chromium-1208 已缓存) headed 能过 Cloudflare，但抓 web 源码用 CDN curl 更省事。

详见 project-ax-cowork-ux（若已建）。本机环境：darwin，Playwright 在 /usr/bin/python3。
