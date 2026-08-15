---
name: feedback_logic-not-screenshot-captions
description: 要"交互逻辑/规范"时交 code-grounded 状态机+真实录屏验证，不是给截图旁边写字
metadata:
  type: feedback
---

用户要"交互逻辑/交互规范/执行编排规范"时，**营销/商店截图里没有逻辑**——把截图摆进 Figma 旁边写标注，会被判"你都没有真实的逻辑"(2026-06-08 ax-cowork-ux 实际发生)。

**Why:** 逻辑=状态/触发/转移/规则，只存在于①真实代码(逆向 web bundle/APK/app.asar 拿 reducer/state machine/事件白名单/真实标识符) ②真实动态录屏(实机抽帧看转场/状态序列)。静态营销图只能证组件存在性，且视觉会骗人(本次营销图助手字体 serif，实机录屏证实是 sans)。

**How to apply:** 交付交互规范时主交付 = code-grounded 状态机(像 cowork-interaction-logic-spec.md 那样带真实标识符/stop_reason/SSE 事件/审批 verbs×scope) + 真实录屏抽帧逐状态验证(ffmpeg 抽帧, 标 [verified-录屏]/[code]/[inferred])；截图只作组件视觉锚点。缺真实源(APK/录屏)时显式标 inferred 并主动要录屏，别用截图标注冒充逻辑。关联 [[feedback_interaction-spec-scenario-storyboards]] [[feedback_1to1-ui-replication-method]]。
