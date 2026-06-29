# explain-like-im-five

A Claude Code skill for learning anything — ask about a cert module, throw a concept at it, or paste your notes. Explained like you're five, saved to your personal vault.

## Usage

```
/eli5 <topic>                        explain any concept
/eli5 course <cert-name>             full ELI5 study guide for a cert
/eli5 course <cert-name> --quiz      study guide + practice questions
```

Or just ask naturally — no slash command syntax required:

```
explain module 1 of ServiceNow Advanced Fundamentals
walk me through the ITSM section of the ServiceNow CSA
explain week 3 of the AWS Solutions Architect course
```

Claude will search for official content, fetch what's publicly accessible, and explain it ELI5-style. For platforms that require login (Udemy, Coursera, edX, LinkedIn Learning), paste your notes or transcript and it'll explain that instead.

## Examples

```
/eli5 TCP handshake
/eli5 how garbage collection works
/eli5 course databricks
/eli5 course servicenow --quiz
explain module 2 of Google Data Analytics
walk me through the change management section of ITIL 4
```

## Vault

When you save, eli5 asks **where** and in **what format**:

- **This folder** (where eli5 is running) — the default; press enter to accept
- **`~/.claude/eli5-notes/`** — a global stash across projects
- **A path you provide**
- **Microsoft Word** (`.docx`, via pandoc or python-docx)
- **Notion** (via the Notion MCP tools)

For the markdown options, explanations land in `library/`, course guides in `library/courses/`, and module explanations in `library/courses/<cert>/<module>.md` under the chosen base. All markdown files — grep, browse, or share them freely.

## Works with any AI coding agent

The eli5 behavior is defined once in [`AGENTS.md`](AGENTS.md) — the single source of truth. Each agent reads it through its own conventions:

| Agent | Instructions | Slash command |
|-------|--------------|---------------|
| Claude Code | `CLAUDE.md` → `AGENTS.md` | `skills/eli5/SKILL.md` (`.claude/skills/eli5/SKILL.md` symlinks to it) |
| Codex | `AGENTS.md` (native) | `.codex/prompts/eli5.md` |
| Gemini CLI | `GEMINI.md` → `AGENTS.md` | `.gemini/commands/eli5.toml` |
| Cursor | `AGENTS.md` (native) | `.cursor/commands/eli5.md` |
| GitHub Copilot | `.github/copilot-instructions.md` → `AGENTS.md` | — |
| Cline | `.clinerules` → `AGENTS.md` | — |
| Windsurf | `AGENTS.md` (native) | — |

`CLAUDE.md`, `GEMINI.md`, `.clinerules`, and `.github/copilot-instructions.md` are symlinks to `AGENTS.md`, so editing `AGENTS.md` updates every agent at once. The slash-command files are thin wrappers that defer to it. To change behavior, edit `AGENTS.md` only.

## Install

**As a Claude Code plugin (recommended):**

```
/plugin marketplace add kidskoding/explain-like-im-five
/plugin install eli5
```

Then use `/eli5` in any project, or just ask naturally.

**As a project:** clone the repo and open it as your working directory. Claude Code reads `.claude/skills/`, and Codex/Cursor/Windsurf read `AGENTS.md` natively. `AGENTS.md` ships alongside, so every agent's entry point can defer to it.

**As a global Claude Code skill:** copy `skills/eli5/` **and** `AGENTS.md` into `~/.claude/skills/` and `~/.claude/` respectively (the skill reads `AGENTS.md`).
