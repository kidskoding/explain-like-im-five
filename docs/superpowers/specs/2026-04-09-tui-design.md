# ELI5 TUI — Design Spec

**Date:** 2026-04-09

---

## Overview

A Go TUI (terminal user interface) built with bubbletea that provides a learning-focused interface for the eli5 skill. The TUI calls the Claude API directly using the existing `.claude/skills/eli5.md` as its system prompt. Explanations are rendered with progressive reveal — each section unlocks one at a time — making it feel like a learning tool rather than a query interface.

---

## Launch

```bash
# Development
go run .

# Production (after go install)
eli5
```

The binary name is `eli5`. The module is `github.com/anirudh/explain-like-im-five` (or local path — set in go.mod).

---

## Tech Stack

- **bubbletea** — TUI framework (model/update/view)
- **lipgloss** — terminal styling (colors, borders, layout)
- **Anthropic Go SDK** (`github.com/anthropics/anthropic-sdk-go`) — streaming API calls
- **Go 1.22+**

---

## Screens

### 1. Home

The entry point. Shows a text input and a list of recent vault entries.

```
┌─ explain like i'm five ─────────────────────────┐
│                                                   │
│  What do you want to understand?                  │
│  > tcp handshake█                                 │
│                                                   │
│  Recent                                           │
│  › garbage-collection                             │
│    hashing-one-way                                │
│    binary-search                                  │
│                                                   │
│  [enter] explain  [c] course  [v] vault  [q] quit │
└───────────────────────────────────────────────────┘
```

**Keybinds:**
- `[enter]` — navigate to TopicReveal with the typed topic
- `[c]` — navigate to CourseEnroll (clears input, shows cert prompt)
- `[v]` — navigate to Vault browser
- `[q]` / `ctrl+c` — quit
- `[↑↓]` — navigate recent list (pressing enter on a list item opens it in TopicReveal read-only)

**Startup:** Reads `library/` and `library/courses/` to populate recent list (sorted by file mod time, newest first, max 10 entries).

---

### 2. Topic Reveal

Streams an explanation from Claude and reveals it section by section.

```
┌─ TCP Handshake ────────────────────────── 1/4 ──┐
│                                                   │
│  ▸ THE ANALOGY                                    │
│  Imagine two strangers on opposite ends of a      │
│  room who want to have a conversation...          │
│                                                   │
│  ▸ WHAT'S ACTUALLY HAPPENING          [locked]    │
│  ▸ GO DEEPER                          [locked]    │
│  ▸ NOW YOU CAN UNDERSTAND             [locked]    │
│                                                   │
│  [space] unlock next  [q] back                    │
└───────────────────────────────────────────────────┘
```

**States:**
- `streaming` — section 1 is being written token by token. `[space]` disabled.
- `reading` — section 1 complete, sections 2–4 locked. `[space]` unlocks next.
- `complete` — all 4 sections visible. Save prompt appears.
- `readonly` — opened from vault. No save prompt, no streaming.

**Section detection:** The Claude client detects boundaries by scanning the stream for the section headers defined in the skill (`### 1.`, `### 2.`, `### 3.`, `### 4.`). When a boundary is found, the current section is marked complete.

**Save prompt** (appears after final unlock):
```
  Saved to library/tcp-handshake.md  ✓
```
Auto-saves on final unlock — no confirmation needed. Confirms with one line.

**Keybinds:**
- `[space]` — unlock next section (only when current section fully streamed)
- `[q]` — back to Home
- `[↑↓]` / scroll — scroll within a long section

---

### 3. Course Enroll

Shows the cert name and its exam domains before starting. One streaming call to get the domain list.

```
┌─ Course: Databricks Associate ──────────────────┐
│                                                   │
│  Exam domains:                                    │
│  1. Apache Spark Core        (high)               │
│  2. Delta Lake               (high)               │
│  3. MLflow                   (medium)             │
│  4. Cloud Integration        (low)                │
│  5. Databricks Architecture  (medium)             │
│                                                   │
│  5 domains · ~25 min                              │
│                                                   │
│  [enter] start  [q] back                          │
└───────────────────────────────────────────────────┘
```

**Flow:** User arrives from Home by pressing `[c]`, which switches the Home input label to `"Course name: "`. User types a cert name and hits `[enter]`, navigating to CourseEnroll with the cert name. Domain list streams in. Once complete, `[enter]` navigates to CourseChapter for domain 1.

---

### 4. Course Chapter

One domain at a time, same progressive reveal as TopicReveal but with course-specific sections.

```
┌─ Apache Spark Core ──── Domain 1 of 5 ──────────┐
│                                                   │
│  ▸ ANALOGY                                        │
│  Like a kitchen with many chefs, each handling   │
│  a different part of the meal simultaneously...  │
│                                                   │
│  ▸ WHAT IT IS                         [locked]    │
│  ▸ KEY CONCEPTS                       [locked]    │
│  ▸ KEY TAKEAWAYS                      [locked]    │
│                                                   │
│  [space] unlock  [→] next domain  [q] back        │
└───────────────────────────────────────────────────┘
```

**Sections per domain:** Analogy, What it is, Key concepts, Key takeaways (matches course mode structure in skill file).

**Navigation:**
- `[space]` — unlock next section (same as TopicReveal)
- `[→]` — next domain (available after all sections unlocked, or skip)
- `[←]` — previous domain
- `[q]` — back to CourseEnroll

**Saving:** After the last domain's last section is unlocked, auto-saves the full course guide to `library/courses/<slug>.md`.

---

### 5. Vault Browser

Searchable list of all saved explanations.

```
┌─ Vault ─────────────────────────────────────────┐
│  /search█                                         │
│                                                   │
│  Topics                      Courses              │
│  › tcp-handshake             databricks-assoc     │
│    garbage-collection        servicenow-csa       │
│    hashing-one-way                                │
│    binary-search                                  │
│                                                   │
│  [enter] open  [/] search  [q] back               │
└───────────────────────────────────────────────────┘
```

**Search:** `/` focuses search input, filters list live as user types (matches filename + tags in front matter).

**Open:** `[enter]` on a topic opens it in TopicReveal (readonly mode). `[enter]` on a course opens CourseEnroll in readonly mode showing all domains already explained.

---

## File Structure

```
cmd/eli5/main.go                  entry point, initializes router
internal/
  router/
    router.go                     screen manager, navigation commands
  screens/
    home/
      home.go                     Home screen model
    topic_reveal/
      reveal.go                   TopicReveal screen model
    course_enroll/
      enroll.go                   CourseEnroll screen model
    course_chapter/
      chapter.go                  CourseChapter screen model
    vault/
      vault.go                    Vault browser screen model
  claude/
    client.go                     Anthropic SDK wrapper, streaming, section detection
  vault/
    vault.go                      read/write library/ files, front matter parsing
go.mod
go.sum
```

---

## Claude Integration

**`internal/claude/client.go`** responsibilities:
- Read `.claude/skills/eli5.md` at startup (once, cached)
- Use file contents as system prompt for all API calls
- Stream responses token by token via Anthropic Go SDK
- Detect section boundaries by scanning for `### 1.` / `### 2.` / `### 3.` / `### 4.` headers in the accumulated stream
- Emit bubbletea `Msg` types: `TokenMsg`, `SectionCompleteMsg`, `StreamDoneMsg`, `StreamErrMsg`

**API key:** Read from `ANTHROPIC_API_KEY` environment variable. If missing, show error on Home screen: `"Set ANTHROPIC_API_KEY to get started."`

**Model:** `claude-sonnet-4-6` (configurable via `ELI5_MODEL` env var).

---

## Vault Integration

**`internal/vault/vault.go`** responsibilities:
- `List() ([]Entry, error)` — read all `.md` files from `library/` and `library/courses/`, parse front matter, return sorted by mod time
- `Save(slug, content, frontMatter) error` — write to correct path based on whether it's a topic or course
- `Read(slug) (string, error)` — read raw file content for readonly view
- `Search(query string) []Entry` — filter entries by slug + tags

Front matter is YAML, parsed with a minimal inline parser (no external dependency needed for simple key: value format).

---

## Error Handling

| Scenario | Behavior |
|---|---|
| `ANTHROPIC_API_KEY` not set | Home screen shows setup message, disables input |
| `.claude/skills/eli5.md` not found | Falls back to hardcoded system prompt (same content) |
| Stream error mid-explanation | Show `"Connection interrupted — [r] retry  [q] back"` inline |
| Vault write failure | Show `"Couldn't save to vault"` warning, don't crash |
| Empty vault | Vault browser shows `"No explanations yet — go learn something."` |

---

## Out of Scope (v1)

- `--quiz` flag in TUI (course mode quiz questions)
- Progress tracking across sessions
- Config file (everything via env vars for now)
- Natural language input / web fetch (tracked in README for future)
