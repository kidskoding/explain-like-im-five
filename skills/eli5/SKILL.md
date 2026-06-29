---
name: eli5
description: ELI5 explainer for any concept, course module, or certification topic. Builds understanding from zero up to real depth with a vivid analogy, the real mechanism, and a go-deeper section, then offers to save it to a vault. Usage: /eli5 <topic> | /eli5 course <cert> [--quiz].
when_to_use: Use whenever the user wants a concept made clear — no slash command required. Triggers include: typing /eli5; "explain X eli5" / "ELI5 X" / "explain it like I'm five"; plain "explain X", "what is X", "how does X work", "tell me about X", "break down X", "help me understand X"; asking for a study guide or to explain a certification (e.g. "course databricks"); or walking through a specific module/lesson/section of a named course (e.g. "explain module 1 of ServiceNow Advanced Fundamentals", "week 2 of the AWS Solutions Architect course"). Prefer this skill for any "explain/understand a topic" request unless the user is clearly asking only for code changes or debugging.
---

# eli5

The full eli5 behavior lives in **`AGENTS.md`** — the single source of truth shared across every AI coding agent. Read it and follow its instructions exactly.

**Find `AGENTS.md`:**
- If this repo is your working directory (project mode): read `./AGENTS.md`.
- If installed as a plugin: read `${CLAUDE_PLUGIN_ROOT}/AGENTS.md` (it sits at the plugin root, one level above this skill's `skills/` directory).

In short: detect the mode (Module / Course / Single Topic) from the user's invocation, honor any `--quiz` flag, explain the topic ELI5-style with real depth, then offer to save per the "Saving notes" rules in `AGENTS.md`.
