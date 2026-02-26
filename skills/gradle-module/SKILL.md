---
name: gradle-module
description: Creates a new Gradle module with proper build configuration, dependency setup, and placement in the module hierarchy. Use when the user wants to add a new feature, service, library, or foundation module.
argument-hint: "[module-name] [type: feature|service|library|foundation]"
allowed-tools:
  - Read
  - Write
  - Edit
  - Glob
  - Grep
  - Bash
---

# Gradle Module Creator

Generate a new Gradle module following the project's modular architecture.

## Arguments

- `$0` — Module name (e.g., "notifications-core", "profile-ui")
- `$1` — Module type: `feature`, `service`, `library`, or `foundation`

## Step 1: Gather Requirements

If arguments are missing, ask:
1. **Module name**
2. **Module type** and explain the hierarchy:
   - `feature` → Self-contained UI features (`features/{name}/`)
   - `service` → Business logic, APIs, repositories (`services/{name}/`)
   - `library` → Reusable utilities (`libraries/{name}/`)
   - `foundation` → Core infrastructure (`libraries/foundation/{name}/`)
3. **Platform**: Android Library or pure Kotlin/JVM
4. **Key dependencies**: Existing modules or libraries needed

## Step 2: Find Existing Patterns

Search for a similar module to match conventions. Based on the module type, search the corresponding directory:
- Feature → `features/`
- Service → `services/`
- Library → `libraries/`
- Foundation → `libraries/foundation/`

Pick one existing `build.gradle.kts` from that directory and read it to understand:
- Plugin aliases used
- Namespace convention
- How dependencies are declared (version catalog references)

## Step 3: Enforce Dependency Hierarchy

```
App → Feature → Service → Library → Foundation
```

Validate that requested dependencies only point downward. Warn if a violation is detected.

## Step 4: Generate Files

1. **`build.gradle.kts`** — Using version catalog references, matching existing conventions
2. **`src/main/kotlin/`** — Package directory matching module path
3. **Update `settings.gradle.kts`** — Add `include()` in alphabetical order

## Step 5: Verify

Run to confirm compilation:
```bash
./gradlew :path:to:module:assemble
```

Checklist:
- [ ] Correct directory for module type
- [ ] Dependencies point downward in hierarchy
- [ ] `settings.gradle.kts` updated
- [ ] Version catalog references used (no hardcoded versions)
- [ ] Namespace follows project convention
