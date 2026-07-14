---
name: analyze-bug
description: Investigate a reported bug to its root cause and write a diagnosis report — title, root cause, solution proposals — to docs/bugs/. Use when the user describes a bug, error, or crash and wants it analyzed rather than fixed, pastes a stack trace or error message for investigation, or when another skill forwards bug context for root-cause analysis.
metadata:
  author: luchengchiu
  version: "1.0.0"
---

# Analyze Bug

Turn a bug description into a written diagnosis: what broke, why, and how it could be fixed. The deliverable is the report — analysis ends there, and the user picks an approach and requests the fix separately. Leave every source file untouched.

## Input

The bug description arrives in the invocation arguments: free text, an error message, a stack trace, or context forwarded by another skill. It may also name the report file. If the input carries no usable signal (no symptom, no error, no location), ask for the symptom before investigating.

## Step 1 — Static investigation

Trace the description into the code: locate the error site from whatever anchors the input gives (stack frames, error strings, component or route names), then read outward along the data flow — where the bad value is produced, passed, and consumed — until the symptom is explained.

**Done when** you can state a root cause with `file:line` references — or reading is exhausted and you hold only candidate causes.

## Step 2 — Reproduce in the browser (only when unconfirmed)

A root cause that's obvious from reading (e.g. `notArray.map()` where `notArray` is initialized to `null`) skips this step entirely. When reading leaves you between candidates, confirm empirically:

1. Detect the dev server URL: check the `dev` script in `package.json` and any `PORT` in `.env*`; default to `http://localhost:3000`.
2. If the server responds, open it with the Playwright browser tools, drive the flow described in the bug, and capture the symptom — console errors, failed network requests, wrong rendering.
3. If the server is down, ask the user for permission to start it, then reproduce.

**Done when** the symptom is observed and ties to one cause, or reproduction is impossible with the access you have.

## Step 3 — Verdict: simple or complex

- **Simple** — the fix is localized (one or two spots) *and* only one sensible way to fix it exists. Report a single solution proposal.
- **Complex** — several reasonable designs exist, or the fix touches architecture, state shape, or data flow. Brainstorm widely, then report the **3 optimal approaches**, each with pros/cons tradeoffs, and recommend one.

## Step 4 — Write the report

Save to `docs/bugs/<name>.md` — the name the user gave at invocation, else `<YYYY-MM-DD>-<slug>.md` slugged from the title; create the directory if missing. Then print the full report in the conversation.

```markdown
# <Title — one line naming the defect, not the symptom>

**Confidence:** confirmed | candidate

## Root Cause

<What is wrong and why the symptom follows, with `file:line` references.>

## Solution Proposal            <!-- simple bug -->
<The fix, with the exact locations to change.>

## Solution Approaches          <!-- complex bug -->
### 1. <Approach name>
<How it works.>
**Pros:** … **Cons:** …
### 2. … ### 3. …

**Recommendation:** <which approach and why>
```

When confidence is `candidate` (neither reading nor reproduction confirmed a single cause): rank the candidate causes by likelihood, state for each exactly what evidence would confirm it (a log line, a repro step, a stack trace), and ask the user for that evidence.

**Done when** the file exists in `docs/bugs/` and the same report has been printed for the user.
