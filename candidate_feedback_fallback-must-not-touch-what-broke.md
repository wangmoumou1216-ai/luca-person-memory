---
name: candidate-fallback-must-not-touch-what-broke
description: 兜底/降级代码不能再碰导致失败的那个东西——否则同一个故障二次穿透，防线等于没有
metadata: 
  node_type: memory
  type: feedback
  originSessionId: b684af58-07a2-4a7f-9dab-c86972f9b8cb
  modified: 2026-08-14T07:36:35.843Z
---

**候选（信号③ 真实返工，待裁决）**

2026-08-14 muse CLI 更新功能。为了让"一行渲染失败不连累其它行"，我给 `items.forEach`
加了 try/catch 兜底，catch 里生成一条可见的错误行：

```js
catch (e) {
  d.textContent = `${(it && it.label) || it.id || '未知'} · 这行渲染失败：${e.message}`;
}
```

用"会抛的 `label` getter"做变异测试时，**兜底自己被同一个 getter 又穿了一次**——异常照样逃出
forEach，后面的行照样被吞，我加的防线一行作用没起。修法是 catch 里只碰 `it.id` 且再套一层 try。

**Why**：写兜底时我的注意力在"要显示什么友好信息"，于是自然地去读**出事的那个对象**取字段——
而失败原因恰恰可能就是"读这个对象的字段会炸"。兜底与被兜底的东西共享了同一个失败前提，
这跟「独立性是假设层面的、不是代码分开写」是同一个病：**catch 块与 try 块共享假设，
就不是真兜底**。

**How to apply**：
- 写任何 catch / 降级分支时问一句：**「如果失败原因正是我在这个分支里做的这件事呢？」**
  典型面：读出错对象的属性（坏 getter / Proxy / null 链）、再调一次刚失败的那个函数、
  在磁盘满时写日志、在网络断时上报错误。
- 兜底里用**已经安全落地的值**（进 try 之前就取好的 id / 索引 / 常量），不要现取。
- 验证方式固定：**用"会抛的属性"而不是"字段缺失"做变异**——后者太弱，前者才逼得出二次穿透。

同族：[[feedback_verify-your-verification]]（独立性是假设层面的）、
[[candidate_feedback_assertion-never-ran-is-not-assertion-passed]]（同 session 第三次踩"异常绕过断言"）。
