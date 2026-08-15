---
name: run-tests-before-claiming-done
description: "改完代码、宣称\"done/已验证\"之前，必须先跑项目现有测试套件（bats/swift/pytest 等）"
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 79705b29-d520-4e9d-917b-e04b554becdf
---

改动代码后、在对用户说"修好了/已验证"之前，**必须先跑项目自带的测试套件**（bats / swift test / pytest / run.sh 等），别只靠自己临时写的合成验证。

**Why:** 2026-06-01 改 CLI 提示器，我做了合成 A/B 就宣称完成，但**没跑项目的 bats**。用户追问后才跑，发现 `session_ttl.bats` 因我把 TTL 从 mtime 改成 emitted（正确改动）而挂——测试编码了旧契约。本该改完立刻跑测试就发现。用户这一 session 已多次抓到我"没跑就说 done"。

**How to apply:** 任何代码改动收尾前：① `find` 项目测试（`*.bats`、`Tests/`、`tests/`、`run.sh`）；② 跑一遍；③ 红了先判断是"我引入的 bug"还是"测试编码了我刚改掉的旧行为"——前者修代码，后者改测试并说明理由；④ 全绿再说 done。相关：[[evidence_grounded_debate]]、[[no_confirmation_loops]]。
