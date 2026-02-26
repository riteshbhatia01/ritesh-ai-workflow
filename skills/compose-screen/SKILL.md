---
name: compose-screen
description: Scaffolds a new Jetpack Compose screen with state management, event handling, and preview. Use when the user wants to create a new screen, UI feature, or compose component following Circuit or ViewModel patterns.
argument-hint: "[screen-name] [optional: circuit|viewmodel]"
allowed-tools:
  - Read
  - Write
  - Glob
  - Grep
---

# Compose Screen Scaffolding

Generate a new Jetpack Compose screen with proper architecture, state management, and preview.

## Arguments

- `$0` — Screen name (e.g., "Profile", "Settings", "OrderDetail")
- `$1` — Architecture pattern: `circuit` (default) or `viewmodel`

## Step 1: Gather Requirements

If `$ARGUMENTS` is empty, ask the user:
1. **Screen name**
2. **Architecture pattern**: Circuit UDF or ViewModel
3. **Key UI elements**: List, form, detail view, empty state, etc.
4. **Navigation arguments**: Does it receive any parameters?

If arguments are provided, infer what you can and ask only what's missing.

## Step 2: Find Existing Patterns

Before generating code, search the codebase for existing screens to match conventions:

```
Glob: pattern="**/*Screen.kt" path="features/"
```

Read one or two examples to understand:
- Package naming convention
- How screens are registered
- Dagger/Anvil injection patterns
- Import style (never use fully qualified names)

## Step 3: Generate Files

### Circuit Pattern (default)

Create three files in the appropriate feature module:

**Screen definition** (`{Name}Screen.kt`):
```kotlin
@Parcelize
data class {Name}Screen(
  // navigation arguments
) : Screen {
  data class State(
    // Use ImmutableList, ImmutableMap for collections
    val eventSink: (Event) -> Unit,
  ) : CircuitUiState

  sealed interface Event {
    // UI events
  }
}
```

**Presenter** (`{Name}Presenter.kt`):
```kotlin
class {Name}Presenter @Inject constructor(
  // dependencies
) : Presenter<{Name}Screen.State> {
  @Composable
  override fun present(): {Name}Screen.State {
    return {Name}Screen.State(
      eventSink = { event ->
        when (event) {
          // handle events
        }
      },
    )
  }
}
```

**UI** (`{Name}Ui.kt`):
```kotlin
@CircuitInject({Name}Screen::class, AppScope::class)
@Composable
fun {Name}Ui(state: {Name}Screen.State, modifier: Modifier = Modifier) {
  // Decompose into smaller @Composable functions
  // Do NOT pass State or eventSink to child composables
}

@Preview
@Composable
private fun {Name}UiPreview() {
  {Name}Ui(
    state = {Name}Screen.State(
      eventSink = {},
    ),
  )
}
```

### ViewModel Pattern

**ViewModel** (`{Name}ViewModel.kt`):
```kotlin
class {Name}ViewModel @Inject constructor(
  // dependencies
) : ViewModel() {
  private val _state = MutableStateFlow({Name}UiState())
  val state: StateFlow<{Name}UiState> = _state.asStateFlow()

  fun onEvent(event: {Name}Event) {
    when (event) {
      // handle events
    }
  }
}

data class {Name}UiState(
  // UI state fields (use ImmutableList for collections)
)

sealed interface {Name}Event {
  // UI events
}
```

**Screen** (`{Name}Screen.kt`):
```kotlin
@Composable
fun {Name}Screen(viewModel: {Name}ViewModel = hiltViewModel()) {
  val state by viewModel.state.collectAsStateWithLifecycle()
  {Name}Content(
    state = state,
    onEvent = viewModel::onEvent,
  )
}

@Composable
private fun {Name}Content(
  state: {Name}UiState,
  onEvent: ({Name}Event) -> Unit,
) {
  // UI implementation
}
```

## Step 4: Verify Checklist

- [ ] State and event types are immutable
- [ ] Collections use `kotlinx.collections.immutable` types
- [ ] UI decomposed into small composable functions
- [ ] No `!!` — use `requireNotNull()` or `checkNotNull()`
- [ ] No custom callbacks in Screen or CircuitUiState
- [ ] Imports used, not fully qualified class names
- [ ] Preview composable included
- [ ] Matches existing codebase conventions found in Step 2
