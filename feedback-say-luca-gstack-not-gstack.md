---
name: say-luca-gstack-not-gstack
description: 框架名/路径写全 luca_gstack，严禁缩写成 gstack；旧记忆里的缩写路径要先解析成真实路径再用（luca 2026-07-11 两次点名）
metadata: 
  node_type: memory
  type: feedback
  originSessionId: d2f40f3c-66bf-4d4f-a27c-1b174035c5a6
---

指代框架时写全名 **luca_gstack**（母版 `~/Desktop/luca_gstack`；muse fork 目录名为
`~/Desktop/项目/muse/lucagstack`——历史命名无下划线，但它就是 luca_gstack 的 fork，不是第三个实体）。
**任何场合都不存在独立的"gstack"**：app 代码里的 `GSTACK` 只是变量名，记忆里出现的 `gstack/...`
只是过往速记，不是真实路径。

**Why:** 2026-07-11 luca 两次纠正：①计划期探索 agent prompt 里写"gstack 侧 / gstack/muse-loop/"
被当场打断；②session 末尾再次点名"开始时还在用 gstack 的调研路径"。根因追溯：muse 项目记忆旧条目
用 `gstack/muse-loop/ARCHITECTURE.md` 速记路径，我照抄进 agent prompt 传播了污染（源条目已于当日修复）。

**How to apply:**
1. 对话、计划、commit message、agent prompt 里一律写全名或真实绝对路径。
2. 从记忆/旧文档引用路径时先解析成磁盘真实路径再下发（尤其 spawn agent 的 prompt——错误速记会被 agent 当真路径走）。
3. 发现记忆条目里有 `gstack/` 类缩写路径 → 顺手修成真实路径，别让污染继续传播。
