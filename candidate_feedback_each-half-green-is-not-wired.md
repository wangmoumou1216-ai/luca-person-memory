---
name: each-half-green-is-not-wired
description: 一个功能的两半各自有测试且全绿，不等于接起来是对的——通道名对不上、失败被吞这类缺陷只活在接缝里
metadata:
  type: feedback
---

两半各自绿 ≠ 接起来是对的。**接缝本身需要一个把两半放进同一个进程的测试。**

2026-08-15 muse 限额续跑：渲染层 e2e 把 preload 整个存根掉，主进程测试是 main.js 原文切片。
两套加起来 80 条断言全绿。补上「真 preload + 真 ipcMain + 真 index.html + 真 app.js 同进程跑完整链」
的第三层后，第一次运行就抓到一个真缺陷——`schedWrite` 把写盘异常 try/catch 吞掉、
`sched:save` 照样返回 `ok:true`：**界面说"已安排"，磁盘上什么都没有**。
存根那半永远不会写盘，切片那半用的是内存假 store，两边结构性地看不见它。

**判据**：一个功能如果跨了进程/上下文边界（IPC、preload、worker、子进程、网络），
问一句「我的测试里，这条边界是被**跨过去**了，还是被**存根掉**了？」全被存根 = 接缝零覆盖。

**三个可复用技法**：
- **对端清单从对方原文里抠，别手写**：wire 测试里用正则从 `preload.cjs` 抠出所有
  `ipcRenderer.invoke('X'` / `send('Y'`，逐个注册对端桩。以后加了桥忘了加对端，装置自己会发现；
  手写清单则是又一份会漂移的真值源。
- **装置缺件会伪装成产品缺陷**：我的切片清理正则（`^function currentCli[\s\S]*?\n}\n`）贪婪匹配，
  把紧随其后的 `writeSettings` 一起删了，症状是"写盘不生效"——看起来一模一样像产品 bug。
  切片测试除了「锚失配 exit 2」，还要对切出来的内容做**存在性断言**（该有的函数在不在）。
- **数个数的断言常常零区分度**：`schedList.children.length === 1` 看着合理，但空状态提示也正好是
  1 个子节点 —— 空的和有一条任务的，断言分不开。改查行里真有那条任务的正文。

关联：[[feedback_extracted-module-must-verify-wiring]]（模块+单测全绿≠生效）、
[[feedback_verify-your-verification]]（shim 掉边界＝那侧代码从未执行＝变异它永不转红）。
本条是它们在**跨进程接缝**上的具体形态与解药。
