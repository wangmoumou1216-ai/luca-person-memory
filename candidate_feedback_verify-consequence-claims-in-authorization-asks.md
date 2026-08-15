---
name: candidate-verify-consequence-claims-in-authorization-asks
description: "请求授权时写的\"这不会影响 X\"是承诺物、必须出口前验；机制推对了不等于结论对——程序可以自愿做别的事"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: b684af58-07a2-4a7f-9dab-c86972f9b8cb
  modified: 2026-08-14T02:49:44.560Z
---

**候选（信号②复发，待 luca 裁决）** — 与 [[feedback_verify-params-before-offering-choices]] 同族，
是它的**范围扩展**：那条管的是选项里的**参数**（路径/命令/值），本条管的是授权请求里的**后果断言**
（"这不会影响 X"／"正在跑的东西不受影响"／"可回退"）。两者是同一个道理——**用户会照此拍板的东西，
出口前必须验**——但我上次只把它内化到了参数那一半。

2026-08-14 muse app CLI 更新能力：我在向 luca 请求"验收时真点一次更新"的授权时写了
「正在跑的会话不受影响（claude 换的是 symlink，运行中的进程持旧 inode）」。**inode 那半句在 OS 层面
完全正确**，但结论错了——claude 会轮询自己的二进制路径，发现被换掉就主动重启前台会话。
红队从装着的二进制里 `strings` 出 `will restart on the new version shortly; background jobs continue
uninterrupted`（前台打印 + 紧跟退出调用），daemon.log 另有 12 次自我重启实录。而 muse app 的主场景
就是常驻 pty claude 会话——我等于拿一句假的安全保证去换他的批准。

**这次的新东西不是"我没查证"，是"我查证的对象错了"。** 危险的正是那个**正确的机制**：inode 语义
是真的，它给了我不该有的信心，让我跳过了"这个程序会不会自愿做点别的"这一问。低层机制正确
**推不出**高层程序行为——程序可以主动 opt-in 到任何行为。

**How to apply**：
- 任何**授权/确认请求**发出前，把里面的**后果断言**单独拎出来当承诺物过一遍：这句我验过吗？拿什么验的？
  参数要验，"不会怎么样"同样要验——后者错的代价更大，因为它直接换取批准。
- 当我的信心来源是"我推导的机制"而不是"我观测的行为"时，**这本身就是要停一下的信号**。
  自问：「这个程序有没有可能故意做和机制预期不同的事？」有可能就去看它的实际实现/日志/字符串。
- 承诺物错了要**当场作废那次授权请求**并重新措辞，不能只在后文补一句更正就当已处理
  （见 [[feedback_disclosure-is-not-remediation]]）。

若 luca 认可，建议不新建条目，而是把 [[feedback_verify-params-before-offering-choices]] 的适用面
从"参数"扩写为"用户会照此拍板的一切承诺物（参数 + 后果断言）"。
