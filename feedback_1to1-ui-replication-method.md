---
name: 1to1-ui-replication-method
description: 1:1 复刻真实产品 UI（Figma/HTML）的方法 + 视频取证 + OD claude DS——逐像素别估值
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 5173866e-3a14-4bf1-9097-53bb687273e7
  modified: 2026-07-22T07:44:12.061Z
---

复刻真实产品 UI（如 Claude Cowork）要做到「丝毫不差」时，**别手画/估值，去抽确切源**：

1. **真实 SVG 图标**：源页面多用 SVG sprite（`<symbol id="ic-x">`+`<use href>`）。抽每个 symbol→拼成独立 `<svg>`→Figma 用 **`figma.createNodeFromSvg(svgString)`** 原样导入（resize 到 .ic 尺寸如 18×18，按用法 string-replace stroke 颜色）。**绝不手画 Ellipse+Line 近似**——用户一眼看出"icon 有问题"。
2. **精确字号/间距/颜色/圆角**：直接读源 CSS 的字面值（:root token hex、各 class 的 px padding/gap/font-size/line-height/radius），**逐值对，不 eyeball**。把 CSS dump 给搭建 agent 当唯一真值。
   **⚠️ 升级（2026-07-22，读 CSS 还不够）：CSS 字面值 ≠ 渲染真值，源是本地 HTML 时必须起服务跑起来查活 DOM。**
   `python3 -m http.server` →（Chrome 扩展不能开 `file://`）→ `getBoundingClientRect()` + `getComputedStyle()` 逐元素取**实测几何**。
   CSS 读不出来的东西：**① 文案本身**（我把 tab 建成 `Sales Leads`，真实是 `Leads`——这才是右侧 `Director` 被切掉的真因，不是宽度）
   **② 计算后尺寸**（内容宽实测 384 而非 390，因为有 6px 滚动条）**③ margin 叠加后的真实行高**（我用 `itemSpacing:0` 吃掉了 `.l2{margin-top:5}`/`.rowfoot{margin-top:6}`，行高偏矮 17px）
   **④ JS 动态渲染的内容**（静态抽 HTML 文本会全部返回空——所有检查项齐刷刷 0 就是"脚本没考验到东西"的信号，数据在 `const OBJ` 里）。
   判据：**凡"1:1"任务，把"能不能跑起来查"当第一选择，读 CSS 只是拿不到运行时的降级方案。**
3. **对真实素材逐元素核**，不只对文字规范：用户的真实截图(image-cache)+视频帧是 ground truth。本 session 因先对文字规范、估图标/主题(浅↔深)、漏分段头(Thought process 开头)，被用户逐条挑了多轮才对。

**视频取证**：用户给 .mov 录屏 → playwright 自带 ffmpeg 解不了(精简版)；用 macOS 原生 **Swift + AVFoundation**（AVAssetImageGenerator）抽帧成 PNG 再逐帧读（脚本见 /tmp/extract_frames.swift 思路）。

**OD（open-design）复刻 Anthropic/非 FxUI 产品**：OD 自带 **`claude` (Anthropic) design system**（暖陶土+clean editorial）= 真实 Cowork 调性；绑它。且 **显式覆盖 open-design/figma-layer skill 默认的 FxUI 橙叠加**（用户要纯 Anthropic 时零 #FF8000、零 FxUI 语义色，主按钮深色）。

**Figma 字体可用性（2026-07-22 实测，1938 个族，省下每次试错）**：`Georgia` / `Times New Roman` / `Helvetica` / `Helvetica Neue` / `Arial` / `SF Pro Text` / `Lyon Text` / `Songti SC` **全都没有**——网页 CSS 里最常见的那批系统字体在 Figma 里基本落空。实际可用替身：衬线 `Source Serif 4`(16 style，Notion/Lyon 风最近) / `Source Serif Pro` / `PT Serif` / `Merriweather` / `Playfair Display`；无衬线 `SF Pro`(44 style，Apple 系统字体本尊) / `Inter` / `Roboto` / `Noto Sans`；等宽 `IBM Plex Mono` / `JetBrains Mono` / `Roboto Mono` / `Source Code Pro`。
**样式名空格陷阱**：`Inter` 是 **`Semi Bold`（带空格）**，`SF Pro` 是 **`Semibold`（不带）** —— 硬编码必抛 `Cannot write to node with unloaded font`。永远先 `listAvailableFontsAsync()` 查了再 load。

**Why**：用户对 1:1 复刻的标准是逐像素；近似图标/估值/错主题/漏结构都会被退回返工。
**How to apply**：接到「严格按 X 的产出 1:1 做图」→ 先抽 X 的真实 SVG+确切 CSS+真实截图/视频，再据此精确搭，别凭印象。关联 [[interaction-spec-scenario-storyboards]]、[[claude-source-access]]。

---

## 并入：feedback_regenerate-from-data-not-source-ui（2026-07-10 治理合并，内容原样保留）

---
name: regenerate-from-data-not-source-ui
description: "给参考产物+规范要\"严格按规范重建\"时，先抽纯数据丢掉源UI、产出每值溯源到规范token不掺模型私货、并诚实交代是谁生成的"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 4a96222f-9d72-4623-9c40-c3f159cedf8f
---

用户给一个**参考产物（HTML/设计稿）+ 一份规范**，要求"按规范重新生成 / 脱离模型能力 / 严格按规范"时：

1. **抽纯数据，丢源 UI**：只提取内容数据（字段/文本/结构语义），**剥光源产物的布局、类名、组件、配色**。绝不把规范里没有的构件从源产物搬过来（本次我把源 HTML 的 `.sop-heading`/`.talk-summary` 直接搬进"规范产出"，被当场抓出"你收到了 UI 干扰"）。需要时先把数据落成独立文件（如纯内容 JSON，零视觉字段）再重建。
2. **每个产出值溯源到规范 token，剔除模型私货**：不加规范没有的视觉（本次混进了 `box-shadow`、`letter-spacing:-0.5px`、被我私自收紧的 padding）。产出后用脚本自检：所有 hex/尺寸是否都在规范 token 集内、0 裸值。
3. **对"谁生成的"保持诚实**：别把我手写 CSS 贴合规范的产物说成"agent/规范跑出来的"。用户就是要隔离"规范效果 vs 模型能力"——主动提出起**独立 subagent**只喂规范+数据来真实验证，而不是替规范背书。
4. **规范缺件就 flag，不要用品味默默补**：规范真没有的组件（如 Notion 设计系统无 alert/quote/section-icon），明说是 gap + 我做了近似映射，让用户决定补不补。

**Why:** 用户在测"规范本身够不够、产出是不是真按规范"，任何被源 UI 带偏、被模型审美加料、或来源含糊的产出都会污染这个测试，导致结论失真和返工（本 session 因此重做两轮）。

**How to apply:** 凡"按规范/按模板/1:1 复刻/独立复现"类任务——先数据后视觉、值值溯源、来源透明、缺件上报。与 [[feedback_1to1-ui-replication-method]]、[[feedback_redteam-own-analysis-before-shipping]]、[[feedback_verify-your-verification]] 互补。
