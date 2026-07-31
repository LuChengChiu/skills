---
name: improve-skill
description: Turn feedback about a skill's output in this session into an edit to that skill's SKILL.md.
disable-model-invocation: true
metadata:
  author: luchengchiu
  version: "1.0.0"
---

# Improve Skill

A skill ran in this session and the user pushed back on what it produced. Decide whether that pushback is an **incident** — this run, this input, done — or **policy** the skill must carry forward, and encode only the policy.

The feedback is in this conversation. Never read transcript files.

## Step 1 — Name the target

The invocation arguments name the skill (`/improve-skill analyze-bug`). Absent that, it's the skill invoked earlier in this session; if several ran, the one the feedback is about.

Done when a single skill name is fixed. If no skill ran this session, or two are equally plausible, ask — do not guess.

## Step 2 — State the complaint back

One line, in your own words, naming what the skill did and what it should have done instead.

Done when the user has seen that line. A misread complaint makes every step after it wasted work, and this is the cheapest place to catch it.

## Step 3 — Incident or policy

The gate. Run it before opening any file.

**Policy** — the feedback improves a *different* run on *different* input:

- a missing step, a wrong default, a bad output shape, an absent guardrail
- recurrence language: "again", "always", "every time", "you keep"
- aimed at how the skill works, not at what this run happened to touch

**Incident** — change nothing, say which line below applies:

- tied to this input: wrong ticket, wrong file, one repo's quirk
- a taste call made once, reversible next week
- about how you work in general rather than this skill — that is a `feedback` memory, not a SKILL.md edit. Offer to write it there.

**Already covered** — the SKILL.md says the right thing and it got ignored. Sharpen that line: make it imperative, give it a checkable completion criterion, move it into the step it governs. A second line repeating the first lengthens the file and weakens both.

Done when every distinct piece of feedback is classified. Report the incidents you dropped and why — silent dropping reads as an oversight.

If nothing is policy, stop here. A skill that grows on every invocation is a skill nobody reads.

## Step 4 — Find every copy before you edit

Skills are copied between the source repo and `~/.claude/skills/`, so editing one copy leaves the other stale and the two **drift**.

```bash
grep -rl "^name: <skill-name>$" ~/.claude/skills ~/skills --include=SKILL.md
```

Done when every hit is listed. Every one gets the identical edit in Step 5.

Any path under `~/.claude/plugins/` is plugin-managed and overwritten on update. Report the path and stop — an edit there is lost work.

## Step 5 — Propose, then apply

Show the diff and one line of rationale per change. Do not write until the user approves.

On approval: apply to every path from Step 4, then bump `metadata.version` — patch for wording, minor for a new rule or step.

Edit in place within the existing structure, inside the step the change governs. A rule appended to the bottom of the file is a rule the agent reaches last.
