---
name: eli5
description: Explain any concept, course module, or certification topic ELI5-style. Fetches official content when possible. Usage: /eli5 <topic> | /eli5 course <cert> [--quiz] | natural language like "explain module 1 of ServiceNow Advanced Fundamentals"
---

The full eli5 behavior lives in `AGENTS.md` at the project root — the single source of truth shared
across all AI coding agents. Read `AGENTS.md` and follow its eli5 instructions exactly.

In short: detect the mode (Module / Course / Single Topic) from the user's invocation, honor any
`--quiz` flag, explain the topic ELI5-style with depth, and offer to save to `library/` per the rules
in `AGENTS.md`.
