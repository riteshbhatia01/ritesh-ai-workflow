---
name: daily-standup
description: Generates a standup update from recent git commits, open PRs, and current branch status. Use when the user asks for a standup, wants to know what they worked on, or needs a daily update.
disable-model-invocation: true
allowed-tools:
  - Bash
  - Read
  - Glob
---

# Daily Standup Generator

Generate a standup update from your recent activity.

## Step 1: Gather Context

Collect the following automatically:

**Recent commits** (last 24 hours):
!`git log --author="$(git config user.name)" --since="24 hours ago" --oneline --all 2>/dev/null || echo "No recent commits"`

**Open PRs:**
!`gh pr list --author="@me" --state=open --limit=10 2>/dev/null || echo "Could not fetch PRs"`

**Current branch:**
!`git branch --show-current 2>/dev/null`

**Uncommitted changes:**
!`git status --short 2>/dev/null | head -10`

## Step 2: Format the Update

Using the context above, generate a standup in this format:

```
**Yesterday:**
- [summarize each commit/PR as a single bullet]
- [group related commits into one item]

**Today:**
- [infer from open PRs and current branch]
- [mention PRs waiting for review]

**Blockers:**
- [PRs with failing CI]
- [PRs waiting on review for 2+ days]
- None (if no blockers)
```

## Rules

- One line per bullet, keep it concise
- Include PR numbers for specificity
- Skip merge commits and automated commits
- Group small related commits on the same PR
- Ask the user if they want to add or remove anything before finalizing
- Output plain text suitable for pasting into Slack or a standup bot
