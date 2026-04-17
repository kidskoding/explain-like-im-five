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

## Install

Copy `.claude/skills/eli5.md` to your `~/.claude/skills/` directory to use this skill globally across all projects.
