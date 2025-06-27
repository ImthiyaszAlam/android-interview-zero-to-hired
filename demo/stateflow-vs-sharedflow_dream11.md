**Interview Question**
What’s the difference between `StateFlow` and `SharedFlow` in Kotlin Coroutines?

🏢 **Asked in:**
**Dream11**

---

## 🧩 Context: Why Interviewers Ask This

Both `StateFlow` and `SharedFlow` are hot flows used in MVVM architecture.
Many devs confuse them or don’t know **when to use what** — that's a red flag in interviews.

Let’s battle them.

---

## 📌 Core Difference Checklist

| Feature/Behavior        | `StateFlow`                         | `SharedFlow`                           |
| ----------------------- | ----------------------------------- | -------------------------------------- |
| Default replay          | 1 (latest value)                    | 0 (can be configured)                  |
| Initial value required? | ✅ Yes                               | ❌ No                                   |
| Designed for...         | **State holding (UI state)**        | **One-time events (snackbar, nav)**    |
| Emits only latest?      | ✅ Yes                               | ❌ Can emit multiple past values        |
| Subscription behavior   | New collector gets **latest value** | Only gets new values (unless replayed) |
| Backpressure handling   | N/A (replaces value)                | Configurable buffer strategy           |
| Use in Compose          | `collectAsState()`                  | `collectLatest()` + `LaunchedEffect`   |
| Lifecycle-aware?        | ❌ No, must use lifecycleScope       | ❌ No, same handling required           |

---

## 🔥 Real Usage Examples

### ✅ When to use `StateFlow`

```kotlin
val uiState = MutableStateFlow<LoginUiState>(LoginUiState.Loading)
```

> For screens that show **current state**: loading, success, error.

---

### ✅ When to use `SharedFlow`

```kotlin
val navEvents = MutableSharedFlow<NavigationCommand>()
```

> For **one-time actions**: showing snackbar, navigation, toasts.

---

## 🚫 Common Developer Mistakes

* Using `StateFlow` for navigation or snackbars → causes repeated actions on recomposition.
* Forgetting to handle SharedFlow's replay/cache — ends up emitting **nothing**.
* Treating them as the same thing just because both are Flows.

---

## ✅ How to Frame It in Interview

> “`StateFlow` is ideal for representing UI state — like loading, content, error.
>
> `SharedFlow` is built for one-time consumable events, like showing a snackbar or navigating.
>
> I avoid using LiveData for events now, because SharedFlow solves event replay more reliably.”

---

## 💣 Where This Goes Wrong in Real Projects

* Devs use `StateFlow` to navigate — on every recomposition, `LaunchedEffect` triggers again, and screen keeps popping.
* Another team forgot to use `replay=1` in SharedFlow — events were never consumed if listener attached late.

---

## 🎯 Pro Tip – To Stand Out

* Use `EventBus` pattern with `SharedFlow` for central event handling.
* Always scope flows with `repeatOnLifecycle` to avoid leaks and avoid stale collection.
* Know that `SharedFlow` can be **cold** at first if replay/buffer isn't set — easy to miss.

---

## 🔍 If You Forget in Interview

Say:

> “I remember that `StateFlow` is used for state and needs an initial value.
> `SharedFlow` is used for events. I can walk through a real code example to explain.”

---

## ⏩ TL;DR – 1-Min Summary

* **StateFlow** → For UI state. Requires initial value. Emits latest.
* **SharedFlow** → For one-time events. No initial value. Configurable.
* Don’t use StateFlow for toasts/snackbars — use SharedFlow.
* Scope both with lifecycle to avoid memory leaks or missed events.

