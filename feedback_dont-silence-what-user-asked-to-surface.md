---
name: feedback_dont-silence-what-user-asked-to-surface
description: "别把状态字段解读成\"用户的决定\"，更别据此给用户明确要的提示加自动消音开关"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: b684af58-07a2-4a7f-9dab-c86972f9b8cb
  modified: 2026-08-14T06:51:52.180Z
---

luca 要的是「codex 和 claude 的 cli 如果发现哪个需要更新，就提示我更新哪个」。我在 codex 的
`~/.codex/version.json` 里看到 `dismissed_version: "0.147.0"`，就推断成「**luca 本人**在 8-13
主动忽略过这个版本」，据此写进产品逻辑：`dismissed_version === latest` 就**不亮红点**，还在计划里
写"更新它等于推翻你自己那次决定"。luca 当场「不对，不对」。（2026-08-14）

**两层错，第二层更严重：**
1. 我对**他的行为**下了断言，而我从没验证过那个字段是谁写的、怎么写的——它完全可能是 CLI 自己
   提示过一次后自动写的。从一个 JSON 字段推断人的意图，本来就没资格。
2. 更糟的是我把这个臆测**做成了开关**：给一个"发现更新就提示我"的功能，加了个会自动消音的条件。
   等于把他要的东西按我猜的他的想法关掉一半。

**Why**：这类错误伪装成"体贴"（尊重用户之前的选择、避免打扰），所以自查时不容易触发警报——
它看起来像加分项而不是越权。但净效果是**用户明确要的能力被我的推断削弱了**。

**How to apply**：
- 状态文件里的字段只当**事实**读（版本号、时间戳），**不当意图**读。想知道用户的决定，问他，
  或者干脆不依赖它。
- 给功能加**任何**抑制/折叠/静默/去重逻辑之前，先对一遍原始诉求：这条会不会在用户想看见的时候
  让他看不见？功能的核心诉求是"让我知道 X"时，默认不加自动消音——需要静音让他自己关。
- 措辞上也别把臆测说成他的既成决定（"你已忽略此版本"）——那会让一个我编的前提读起来像共识。

同族：[[feedback_intent-first-over-option-menus]]（别拿我的偏好当他的选项空间）、
[[candidate_feedback_verify-consequence-claims-in-authorization-asks]]（承诺物出口前必验）。
