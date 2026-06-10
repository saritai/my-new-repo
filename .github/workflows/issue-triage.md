---
emoji: 🏷️
description: Triage newly opened issues by labeling type and priority, flagging duplicates, requesting clarification when unclear, and assigning maintainers.
on:
  issues:
    types: [opened]
  roles: all
permissions:
  contents: read
  issues: read
  pull-requests: read
tools:
  bash: [gh, grep, jq, cat, head, tail, wc, echo, sort, uniq]
  github:
    mode: gh-proxy
    toolsets: [default]
safe-outputs:
  add-labels:
    allowed:
      - bug
      - enhancement
      - documentation
      - question
      - duplicate
      - needs-more-info
      - "priority: critical"
      - "priority: high"
      - "priority: medium"
      - "priority: low"
    max: 4
  add-comment:
    max: 1
  assign-to-user:
    allowed: [saritai]
    max: 2
---

# Issue Triage

You are triaging a **newly opened issue** in `${{ github.repository }}`.

- Issue number: **#${{ github.event.issue.number }}**
- Issue content (title and body, sanitized):

"${{ steps.sanitized.outputs.text }}"

Use the pre-authenticated `gh` CLI for all GitHub reads (for example `gh issue view`, `gh issue list`, `gh search issues`). Make only ONE visible change per category and use ONLY the configured safe outputs.

## Steps

### 1. Classify the type
Read the title and body, then choose the single best type label:
- `bug` — a defect or unexpected behavior
- `enhancement` — a feature request or improvement
- `documentation` — docs additions or corrections
- `question` — a support or usage question

Apply exactly one type label with `add-labels`.

### 2. Assess priority
Estimate impact and urgency, then apply exactly one priority label:
- `priority: critical` — outage, data loss, security, or blocking many users
- `priority: high` — major broken functionality or strong business need
- `priority: medium` — important but not blocking
- `priority: low` — minor, cosmetic, or nice-to-have

### 3. Detect duplicates
Search existing issues for similar reports, e.g.:
`gh search issues --repo ${{ github.repository }} --state all "<keywords>"`
or `gh issue list --repo ${{ github.repository }} --state all --search "<keywords>"`.
If you find a clear duplicate:
- Add the `duplicate` label.
- In your single comment, link the original issue (e.g. `#<number>`) and briefly explain why it appears to be a duplicate.

### 4. Ask clarifying questions when unclear
If the issue lacks the information needed to act on it (no reproduction steps for a bug, no clear problem statement, no expected vs. actual behavior, missing version/environment, etc.):
- Add the `needs-more-info` label.
- Post a single, friendly comment listing the specific questions or details required. Use a short bulleted checklist.

### 5. Assign the right maintainer
Assign the issue to the maintainer(s) responsible for the affected area using `assign-to-user`. Use the routing guide below; if no specific area matches, default to `@saritai`.

> **Team routing** (maintainers — customize this table and the `assign-to-user.allowed` list as your team grows):
>
> | Area | Maintainer |
> |---|---|
> | Default / general | saritai |

## Safe outputs

- Use `add-labels` for the type label, the priority label, and (when applicable) `duplicate` and/or `needs-more-info`.
- Use a single `add-comment` to combine any duplicate note and/or clarifying questions. Keep it concise and friendly.
- Use `assign-to-user` to assign the responsible maintainer(s).
- If the issue is already perfectly clear, correctly labeled, and needs no action, call `noop` with a one-line explanation instead of forcing a change.
