# CriterionWorks — Org-Level GitHub Actions

This repository contains reusable workflows for the **CriterionWorks** GitHub organisation.

## Reusable Workflows

| Workflow | Purpose |
|---|---|
| `workflows/notify.yml` | Email notification for push, PR, issue, and release events |

## How to Add Notifications to a Repo

Add `.github/workflows/events.yml` to the target repo:

```yaml
on: [push, pull_request, issues, release]
jobs:
  notify:
    uses: CriterionWorks/.github/.github/workflows/notify.yml@main
    secrets: inherit
```

## Required Org Secrets

Set at org level (Settings → Secrets and variables → Actions):

| Secret | Value |
|---|---|
| `GMAIL_USER` | `ratansrivastava.work@gmail.com` |
| `GMAIL_PASS` | 16-char Google app password |
