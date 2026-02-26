---
name: changelog
description: Generates a structured changelog from git commits between two references. Use when the user asks for release notes, a changelog, or a summary of changes between versions.
argument-hint: "[from-ref] [to-ref]"
disable-model-invocation: true
allowed-tools:
  - Bash
---

# Changelog Generator

Generate a structured changelog from commits between two git references.

## Arguments

- `$0` — From reference: tag, branch, or SHA (e.g., `v2.1.0`)
- `$1` — To reference (default: `HEAD`)

## Step 1: Collect Commits

If `$ARGUMENTS` is empty, ask:
1. **From**: Which tag, branch, or commit to start from?
2. **To**: Where to end (default: HEAD)

Gather data using the from and to references provided by the user:

```bash
git log <from-ref>..<to-ref> --oneline --no-merges
```

```bash
git log <from-ref>..<to-ref> --format="%h %s" --no-merges
```

Replace `<from-ref>` and `<to-ref>` with the actual values from `$0` and `$1`.

Also try to get PR info:
```bash
gh pr list --state=merged --base=main --limit=50 --json number,title,labels
```

## Step 2: Categorize

Group commits by analyzing messages and changed files:

| Category | Signals |
|----------|---------|
| **Features** | "add", "new", "implement", "create", new modules/screens |
| **Bug Fixes** | "fix", "resolve", "patch", "correct", "bug" |
| **Improvements** | "refactor", "optimize", "update", "enhance", "improve" |
| **Testing** | "test", snapshot updates, test file changes |
| **Infrastructure** | build config, CI, dependencies, tooling, Gradle |

## Step 3: Format

```markdown
## [version] - YYYY-MM-DD

### Features
- Description of feature (#PR)

### Bug Fixes
- Description of fix (#PR)

### Improvements
- Description of improvement (#PR)

### Testing
- Test changes (#PR)

### Infrastructure
- Infra change (#PR)
```

## Rules

- Start each entry with a verb: Add, Fix, Update, Remove
- Include PR number when available
- Skip empty categories
- Combine related commits into single entries
- Order by impact within each category
- Ask user to review before finalizing
