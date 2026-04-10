# explain-like-im-five

A Claude Code skill that explains any CS/technical concept in depth — simple analogy first, then real mechanisms, then deeper insight. Saves every explanation to a personal knowledge vault.

## Usage

```
/eli5 <topic>                        explain a concept
/eli5 course <cert-name>             ELI5 study guide for a cert
/eli5 course <cert-name> --quiz      study guide + practice questions
```

## Examples

```
/eli5 TCP handshake
/eli5 how garbage collection works
/eli5 why is hashing one-way
/eli5 course databricks
/eli5 course servicenow --quiz
```

## Vault

Explanations are saved to `library/`. Course guides go to `library/courses/`. All files are plain markdown — grep, browse, or share them freely.

## Install

Copy `.claude/skills/eli5.md` to your `~/.claude/skills/` directory to use this skill globally across all projects.
