---
name: feedback_assertion-never-ran-is-not-assertion-passed
description: "断言\"没跑到\"会被计成绿——异步测试块里 await 一个 reject 会抛过 ok() 让套件绿着退出"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: b684af58-07a2-4a7f-9dab-c86972f9b8cb
  modified: 2026-08-14T07:36:53.701Z
---

**候选（信号②③，待裁决）** — [[feedback_verify-your-verification]] 的新变体：
已有条目覆盖「查源码字符串而非行为」「shim 掉边界」「变异形态太弱」，本条是**第四种**：
**断言压根没执行**，而没执行在计数上等于没失败。

2026-08-14 muse CLI 更新能力，L11 测失效路径（`latestVersion` 全线抛异常 → 应不崩不亮）：

```js
const boom = await checkCliUpdates({ deps: { fetch: async () => { throw new Error('网络炸了'); } } });
ok('L11 …', boom.items.every(x => !x.updatable && x.err));
```

变异④把实现里的兜底 catch 摘掉，**缺陷确实复现**（函数真的 reject 了），**L11 却是绿的**。
因为 promise reject 时 `await` 直接抛出、穿过整个测试块，`ok(...)` 一次都没被调用——
`fail` 计数没加，套件照常 `PASS=95 FAIL=0` 退出。这条断言从写下那天起就什么都没守。

**Why**：我一直用"断言红不红"当牙口判据，但那默认了「断言总会被执行」。异步 + 顶层块的组合
让这个前提不成立，于是"没红"同时兼容两种截然相反的事实（守住了 / 根本没跑）。

**How to apply**：
- 测**失效路径**（期望不抛、期望降级）时，**必须自己 try/catch 接住**再断言，把"整体 reject 了"
  写成一种显式的失败态；不能裸 `await` 然后断言返回值。
- 变异测试的判据从"有没有 FAIL 行"升级为"**FAIL 行数 + 总数是否守恒**"：总数变少 = 有断言没跑到，
  和转红一样是信号。（本次正是 95→94+1 才对；若变成 94+0 就是断言蒸发了。）
- 通用自检：「这条断言在被测物坏掉时，**会不会根本没有机会执行**？」——超时、reject、
  提前 return、fixture 构造失败都能造成这个。

是变异测试把这条假断言抓出来的——这也再次说明变异不是可选项。

**同 session 第二次（几小时后，另一个文件）**：Phase 3 变异①把渲染改成 `innerHTML` 拼接，
我的变异检查器报"没转红"。真相是 DOM shim 的 `innerHTML` setter 抛出，**整个套件被未捕获异常
崩掉、一条 FAIL 都没打印**，而我的判据是 `grep -E "^FAIL"` ——0 命中于是判"没红"。
第一次是"断言没执行到"，这次是"断言执行了但整个进程死了"，**症状在 grep 眼里一模一样**。
修法同上（自己接住 throw 再断言）。

⇒ 判据固化为两条，缺一不可：**① 有 FAIL 行 ② 总数守恒**（95→94+1 才对；变成 94+0 或 0+0
都是"断言蒸发"，和转红一样必须停下来查）。只看 ① 的检查器本身就是安慰剂。
