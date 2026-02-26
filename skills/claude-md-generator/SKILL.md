---
name: claude-md-generator
description: Generates a CLAUDE.md file for any project by analyzing its structure, build system, conventions, and tooling. Use when setting up Claude Code for a new project or bootstrapping AI-assisted development.
argument-hint: "[project-path]"
allowed-tools:
  - Read
  - Glob
  - Grep
  - Bash
  - Write
---

# CLAUDE.md Generator

Analyze a project and generate a tailored CLAUDE.md configuration file.

## Arguments

- `$0` — Path to the project root (default: current directory)

## Step 1: Detect Project Type

Scan for build/config files to identify the stack:

| File | Stack |
|------|-------|
| `build.gradle.kts` | Android / Kotlin / Gradle |
| `package.json` | JavaScript / TypeScript / Node |
| `Cargo.toml` | Rust |
| `go.mod` | Go |
| `pyproject.toml` / `setup.py` | Python |
| `Gemfile` | Ruby |
| `pom.xml` | Java / Maven |

## Step 2: Analyze the Project

For each detected stack, gather:

1. **Build commands**: How to build, run, and test
2. **Project structure**: Key directories and their purpose
3. **Code conventions**: Linter configs, formatter configs, naming patterns
4. **Dependencies**: How they're managed (version catalogs, lock files, etc.)
5. **Testing**: Test runner, testing libraries, test directory structure
6. **Architecture**: Patterns used (MVC, MVVM, Circuit, Clean Architecture, etc.)
7. **Boundaries**: Directories that should not be modified (build/, generated/, etc.)

## Step 3: Generate CLAUDE.md

Create a CLAUDE.md with these sections:

```markdown
# CLAUDE.md

## Project Overview
[One paragraph: what this project is and does]

## Build and Development Commands
[Actual commands to build, run, test, lint — not generic placeholders]

## Project Structure
[Key directories and what they contain]

## Code Conventions
[Language-specific conventions from linter/formatter configs and code analysis]

## Dependencies
[How dependencies are managed, how to add new ones]

## Testing
[How to run tests, conventions, libraries used]

## Architecture
[Key patterns and principles]

## Things to Avoid
[Directories not to modify, patterns not to use, common pitfalls]
```

## Step 4: Refine

Ask the user:
- "Anything specific you want Claude to know about this project?"
- "Any commands that need special flags or environment setup?"
- "Any directories Claude should avoid?"

Incorporate answers and write the final CLAUDE.md.

## Rules

- Keep it concise and scannable
- Use actual commands found in the project, not generic ones
- Include what NOT to do alongside what to do
- Reference specific config files when mentioning conventions
