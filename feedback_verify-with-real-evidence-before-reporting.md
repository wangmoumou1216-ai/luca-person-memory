---
name: verify-with-real-evidence-before-reporting
description: "Before reporting a fix or feature as done, verify it yourself with real evidence (live API calls, disk checks, external process checks) — not just \"looks correct in code\" or self-reported success."
metadata: 
  node_type: memory
  type: feedback
  originSessionId: 50abfbd1-8d44-4968-be88-d62fad683cce
---

Before presenting a fix, feature, or claim as done, verify it with real, external evidence — not by re-reading the code and asserting it looks right, and not by trusting a subprocess's own self-reported success message.

**Why:** During an extended todo-capsule debugging session, luca said explicitly: "我现在需要你review和测试一下你的代码和需求以及使用场景。现在我不太信任你做好了。在我验证前，你自己验证一遍" (I don't trust that you've done this well — verify it yourself before I do). This wasn't a one-off correction; the working style that followed was validated repeatedly over many hours without objection: real API dispatches through the actual production path, external `ps`/`grep` checks on disk state, deliberately reproducing bugs with genuine hanging processes or dirty git files rather than synthetic mocks. One instance mattered a lot: a worker reported `exitCode: 0` and a plausible-looking success JSON for a memory-write call, but the file had actually landed in the wrong directory — the self-report was confidently wrong, and only an external `grep`/`wc -l` check on the actual file caught it.

**How to apply:** When claiming a bug is fixed or a feature works, actually exercise it end-to-end through the real path (not a bypassed/mocked one) and check the *external* result (file contents on disk, process state via `ps`, a second independent read) rather than trusting the return value or log line the code under test itself produced. This is especially load-bearing right after trust has been shaken by a prior miss — don't let "the code review looks right" substitute for "I ran it and checked."
