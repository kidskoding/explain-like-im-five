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

Explanations are saved to `library/`. Course guides go to `library/courses/`. Module explanations go to `library/courses/<cert>/<module>.md`. All files are plain markdown — grep, browse, or share them freely.

## Works with any AI coding agent

The eli5 behavior is defined once in [`AGENTS.md`](AGENTS.md) — the single source of truth. Each agent reads it through its own conventions:

| Agent | Instructions | Slash command |
|-------|--------------|---------------|
| Claude Code | `CLAUDE.md` → `AGENTS.md` | `.claude/skills/eli5.md`, `.claude/commands/eli5.md` |
| Codex | `AGENTS.md` (native) | `.codex/prompts/eli5.md` |
| Gemini CLI | `GEMINI.md` → `AGENTS.md` | `.gemini/commands/eli5.toml` |
| Cursor | `AGENTS.md` (native) | `.cursor/commands/eli5.md` |
| GitHub Copilot | `.github/copilot-instructions.md` → `AGENTS.md` | — |
| Cline | `.clinerules` → `AGENTS.md` | — |
| Windsurf | `AGENTS.md` (native) | — |

`CLAUDE.md`, `GEMINI.md`, `.clinerules`, and `.github/copilot-instructions.md` are symlinks to `AGENTS.md`, so editing `AGENTS.md` updates every agent at once. The slash-command files are thin wrappers that defer to it. To change behavior, edit `AGENTS.md` only.

## Install

Install the whole repo as a project (or as a Claude Code plugin) so `AGENTS.md` ships alongside the command files — the commands defer to it.

For a global Claude Code skill across all projects, copy the repo's `.claude/skills/eli5.md` **and** `AGENTS.md` into `~/.claude/` (the skill reads `AGENTS.md`).
