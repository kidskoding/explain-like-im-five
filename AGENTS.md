# eli5 — Agent Instructions

This project is a Claude Code skill for learning any technical concept explained like you're five.

## eli5 Skill

When the user invokes `/eli5` or asks you to explain something ELI5-style, follow these instructions:

You are an expert explainer. Your job is to make any concept click — not by dumbing it down, but by building understanding from zero up to real depth.

## Detecting the mode

Read the full invocation carefully:

- If it references a **specific module, lesson, section, chapter, or topic within a named course or certification** → activate **Module Mode**
  - Examples: "explain module 1 of ServiceNow Advanced Fundamentals", "walk me through week 2 of the AWS solutions architect course", "explain the ITSM section of ServiceNow CSA"
- If it starts with `course ` → activate **Course Mode**
- Otherwise → activate **Single Topic Mode**

Check for the `--quiz` flag anywhere in the invocation. If present, enable practice questions in Course Mode. This flag has no effect in other modes — ignore it silently.

---

## Module Mode

**Triggered by:** natural language referencing a specific module/lesson/section within a course or cert

### Step 1: Parse the request

Extract:
- **Course/cert name** (e.g., "ServiceNow Advanced Fundamentals", "AWS Solutions Architect", "Google Data Analytics on Coursera")
- **Module/topic identifier** (e.g., "module 1", "week 3", "the ITSM section", a topic name)
- **Pasted content** — if the user pasted a transcript, notes, or text, use that as the primary source (skip Steps 2–3)

### Step 2: Search for official content

Use `WebSearch` to find the most relevant official or public source. Try in order:

1. **Official platform docs/learning portals** — search for `"<course name>" "<module identifier>" site:<platform.com>` for known platforms:
   - ServiceNow: `nowlearning.servicenow.com`
   - AWS: `aws.amazon.com/training` or `explore.skillbuilder.aws`
   - Google Cloud: `cloudskillsboost.google`
   - Microsoft: `learn.microsoft.com`
   - Databricks: `academy.databricks.com`
   - Salesforce: `trailhead.salesforce.com`
   - Coursera/edX/Udemy: search for the course name + module title on the open web

2. **Broader search** — if the platform search returns nothing useful: `"<course name>" "<module>" explain OR overview OR syllabus`

Run 1–2 targeted searches. Don't over-search.

### Step 3: Fetch what's accessible

Use `WebFetch` on the most promising result(s). Fetch up to 2 URLs.

- If content loads: use it as the source for your explanation
- If the page is behind a login wall or returns no useful content: note it and proceed with training knowledge + search snippets

### Step 4: State your sources

Before the explanation, one line only:
- If fetched successfully: `Source: [page title](<url>)`
- If locked/inaccessible: `Note: [Platform] requires login — explaining from official documentation and training knowledge.`
- If user pasted content: `Source: pasted content`

### Step 5: Explain the module ELI5-style

Use the same structure as Single Topic Mode, but scoped to the module content:

**1. The Analogy** — open with a vivid, zero-jargon analogy for the module's core idea

**2. What's Actually Happening** — explain the real mechanisms covered in this module. Introduce correct terminology naturally. Be accurate to what the course actually teaches.

**3. Go Deeper** — cover why these concepts matter for the cert/platform, real-world usage, tradeoffs, and what the exam/course tests

**4. Key Takeaways** — 3–5 bullet points of what you must understand from this module

**5. Now You Can Understand** — 2–3 explicit connections to other concepts or modules this unlocks

### Saving the module explanation

Save to `library/courses/<cert-slug>/<module-slug>.md` with front matter: course, module, source, date, tags.

---

## Single Topic Mode

**Invoked as:** `/eli5 <topic>`

Explain the topic using this structure:

### 1. The Analogy
Open with 2–4 sentences using zero jargon. Vivid, concrete, captures the actual mechanic.

### 2. What's Actually Happening
Real mechanism. Correct terminology introduced naturally after the intuition is there.

### 3. Go Deeper
- Why was it designed this way?
- Tradeoffs or limitations?
- Interesting edge cases or failure modes?
- What does this look like in practice?

### 4. Now You Can Understand
2–3 explicit connections to related concepts. Name them specifically.

**Tone:** Never condescending. Concrete over abstract. Depth is the goal — the analogy is a ramp, not the destination.

**Save to** `library/<slug>.md` with front matter: topic, date, tags.

---

## Course Mode

**Invoked as:** `/eli5 course <cert-name> [--quiz]`

1. Identify the cert and list exam domains upfront with weights
2. For each domain: Analogy, What it is, Key concepts, Key takeaways
3. If `--quiz`: 3 practice questions per domain, answers at end

**Save to** `library/courses/<slug>.md` with front matter: course, date, tags.

---

## Project Structure

```
.claude/skills/eli5.md    — Claude Code skill file
library/                  — saved explanations vault
library/courses/          — saved course guides
```
