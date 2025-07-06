# Interview Question  
**What’s the difference between `StateFlow` and `SharedFlow` in Kotlin Coroutines?**

**Company:** Dream11  
**Role:** Android Engineer  
**Experience:** 1.8 years  

---

## Context: Why Interviewers Ask This

Both `StateFlow` and `SharedFlow` are hot flows used in MVVM architecture.  
Many developers confuse them or don’t know **when to use what** — that's a red flag in interviews.

Let’s battle them.

---

## Core Difference Checklist

| Feature/Behavior        | `StateFlow`                         | `SharedFlow`                           |
| ----------------------- | ----------------------------------- | -------------------------------------- |
| Default replay          | 1 (latest value)                    | 0 (can be configured)                  |
| Initial value required? | Yes                                 | No                                     |
| Designed for...         | **State holding (UI state)**        | **One-time events (snackbar, nav)**    |
| Emits only latest?      | Yes                                 | Can emit multiple past values          |
| Subscription behavior   | New collector gets **latest value** | Only gets new values (unless replayed) |
| Backpressure handling   | N/A (replaces value)                | Configurable buffer strategy           |
| Use in Compose          | `collectAsState()`                  | `collectLatest()` + `LaunchedEffect`   |
| Lifecycle-aware?        | No, must use lifecycleScope         | No, same handling required             |


---

## Real Usage Examples

### When to use `StateFlow`

```kotlin
val uiState = MutableStateFlow<LoginUiState>(LoginUiState.Loading)
````

> Use when the screen reflects a **current state**: loading, success, or error.

---

### When to use `SharedFlow`

```kotlin
val navEvents = MutableSharedFlow<NavigationCommand>()
```

> Use when emitting **one-time actions**: navigation, snackbars, etc.

---

## Common Developer Mistakes

* Using `StateFlow` for navigation → triggers navigation on recomposition
* Not setting `replay` in SharedFlow → event never received if listener attaches late
* Assuming both are interchangeable just because both are flows

---

## How to Frame It in Interview

> “`StateFlow` is for persistent state like loading or content.
> `SharedFlow` is for one-time UI events like navigation or toasts.
> I avoid LiveData for events now — SharedFlow handles this more reliably with replay configuration.”

---

## Where This Goes Wrong in Real Projects

* Dev used `StateFlow` to trigger navigation → screen kept popping after every recomposition
* Another dev used `SharedFlow` without setting `replay=1` → event missed if collector was late

---

## Pro Tip – To Stand Out

* Use `SharedFlow` with `EventBus` pattern for centralized handling
* Always collect flows with `repeatOnLifecycle` to avoid memory leaks
* Know that `SharedFlow` needs replay or buffer setup to avoid “cold start” issues

---

## If You Forget in Interview

Say:

> “I remember `StateFlow` needs an initial value and holds state.
> `SharedFlow` is for one-time events like toasts.
> I can walk through real code to show how I use both correctly.”

---

## TL;DR – 1-Min Summary

* **StateFlow**: For UI state. Has latest value. Requires init value.
* **SharedFlow**: For one-time events. Configurable replay. No init value.
* Avoid `StateFlow` for events like toast/snackbar → use `SharedFlow`
* Scope both flows carefully to avoid memory leaks and missed events

---

**Part of the Android Interview Kit built from 300+ real interviews with engineers at Flipkart, Swiggy, Dream11, Zomato, Meesho & more.**

📥 [Follow on Medium](https://medium.com/@developerimthiyas)
🤝 [Connect on LinkedIn](https://www.linkedin.com/in/imthiyasalam)

