---
name: jira-bug-analyze
description: Read a Jira ticket URL, distill it into a bug payload, and hand off to analyze-bug — /jira-bug-analyze <jira-ticket-url>.
disable-model-invocation: true
metadata:
  author: luchengchiu
  version: "1.0.0"
---

# Jira Bug Analyze

Pipeline: Jira ticket URL → lean bug payload → `analyze-bug`. This skill only reads and reformats; the diagnosis report is `analyze-bug`'s deliverable.

## Step 1 — Read the ticket

Parse the ticket key from the URL (the `PROJ-123` segment in `/browse/PROJ-123` or a `selectedIssue` query param). Fetch the ticket through the Atlassian Remote MCP's issue-fetch tool (named like `getJiraIssue`, prefixed `mcp__plugin_atlassian_atlassian__` — match against the tools actually exposed). Pull summary, description, and any fields holding steps to reproduce, environment, or error output.

- MCP absent or unauthenticated → stop; tell the user to install and authenticate the Atlassian Remote MCP plugin, then re-run. Fetch the ticket through that MCP only.
- Argument missing or not a Jira ticket URL → stop; ask for a valid ticket URL.

**Done when** the ticket's fields are in hand, or the run has stopped with one of the two messages above.

## Step 2 — Format the payload

Distill the ticket to bug signal, in memory only:

```markdown
# <KEY>: <summary>

**Source:** <ticket URL>

## Description
<description, verbatim where it carries signal>

## Steps to Reproduce
<if present>

## Environment
<if present>

## Error Output
<error messages / stack traces found anywhere in the ticket>

## Attachments
<filenames only, noted as unfetched — omit section if none>
```

Omit any empty section. Skip status, priority, assignee, and comments.

**Done when** every non-empty field above is filled from the ticket.

## Step 3 — Print and hand off

Print the payload in the conversation, then immediately invoke the `analyze-bug` skill with the payload as its argument, naming the report after the ticket key so it lands at `docs/bugs/<KEY>.md`. A payload with thin bug signal still hands off — `analyze-bug` owns that guard.

**Done when** `analyze-bug` has been invoked with the printed payload and the report name `<KEY>`.
