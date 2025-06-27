**Perfect.** Now let’s visualize and flow through the concept like a senior dev thinks — here comes your **Mindmap/Flow-Based Interview Q\&A**.

---

## 🧠 Mindmap Format – Android Interview Q\&A #6

### **Interview Question**

What are the different states in an Android Activity lifecycle, and how do they affect API calls or UI updates?

🏢 **Asked in:**
**Cred**

---

## 🧩 The Problem Most Juniors Face

They memorize this:

> `onCreate() → onStart() → onResume() → onPause() → onStop() → onDestroy()`

But they *can’t explain*:

* Where to start an API call?
* When to stop a video?
* Why a crash happens when screen rotates?

Let’s fix that.

---

## 🧠 Flow-Based Understanding – **The Lifecycle Stream**

```plaintext
App Opens:
 ┌────────────┐      ┌─────────────┐      ┌─────────────┐
 │ onCreate() │ ───▶ │ onStart()   │ ───▶ │ onResume()  │
 └────────────┘      └─────────────┘      └─────────────┘
                                          ▼
                                  [User interacts here]
                                          ▼
 ┌────────────┐      ┌─────────────┐      ┌──────────────┐
 │ onPause()  │ ◀─── │ onStop()    │ ◀─── │ onDestroy()   │
 └────────────┘      └─────────────┘      └──────────────┘
```

### ⏺️ Lifecycle Categories:

* **Active state:** `onResume()` – user is seeing and interacting with the screen.
* **Visible but inactive:** `onStart()` – visible, but user not interacting.
* **Hidden/in background:** `onStop()` – user left or opened another app.
* **Destroyed:** `onDestroy()` – Activity is cleaned up.

---

## ✅ Where to Do What (Real-Life Mappings)

| Task                          | Best Lifecycle Method       | Why?                             |
| ----------------------------- | --------------------------- | -------------------------------- |
| Setup UI, init views          | `onCreate()`                | One-time setup                   |
| Start animations, videos      | `onResume()`                | User is interacting              |
| Fetch fresh API data          | `onStart()` or `onResume()` | App is visible again             |
| Pause videos, unregisters     | `onPause()`                 | Stop heavy operations            |
| Release resources, stop tasks | `onStop()` or `onDestroy()` | App is leaving screen or closing |

---

## 🚫 What Juniors Do Wrong

* API call in `onCreate()` → Result arrives after rotation → app crashes or leaks.
* Forgetting to unregister location or broadcast receivers → battery drain.
* Updating UI in `onStop()` → app crashes with `IllegalStateException`.

---

## ✅ How to Say It in Interview

> “I use `onCreate()` for one-time setups like binding views or initializing ViewModels.
>
> I avoid doing network calls there. Instead, I trigger data loading in `onStart()` or `onResume()`, depending on if the screen needs refresh.
>
> I stop listeners and animations in `onPause()` and `onStop()` to avoid leaks and wasted resources.”

---

## 🧪 Where This Goes Wrong in Real Apps

* **Video continues playing in background** because dev forgot to pause in `onPause()`.
* **Multiple API calls fired** on each screen rotation because data was fetched in `onCreate()`.
* **Location services kept running** even after app was closed — major battery drain.

---

## 🎯 Pro Tips (Senior Level Insight)

* Use `repeatOnLifecycle()` (from lifecycle-runtime-ktx) to **launch coroutines safely**:

```kotlin
lifecycleScope.launch {
    repeatOnLifecycle(Lifecycle.State.STARTED) {
        viewModel.uiState.collect { updateUI(it) }
    }
}
```

* Don’t put coroutines directly in `onCreate()` or `onResume()` without scoping.
* In Compose, use `LaunchedEffect(Unit)` with `LifecycleOwner.lifecycle` if needed.

---

## 📌 Interview Checklist

| Question                           | Your Thinking Should Be…                    |
| ---------------------------------- | ------------------------------------------- |
| Where do I call APIs?              | `onStart()` or `onResume()`                 |
| When do I clean up resources?      | `onPause()` / `onStop()` / `onDestroy()`    |
| Why not call APIs in `onCreate()`? | Risk of double-fetch during rotation        |
| What if Activity gets destroyed?   | Data should live in ViewModel, not Activity |

---

## ⏩ TL;DR – 1-Min Summary

* Lifecycle is not just a list — it's a behavior contract.
* Start heavy work **only when user is seeing screen**.
* Avoid leaks by stopping services/listeners early.
* Use ViewModel + lifecycleScope + repeatOnLifecycle for modern architecture.
* Think like Android: user-visible = do work; not visible = pause/cleanup.

---

Would you like the **next question**?
Shall we continue with another **Mindmap-style** or mix it up again?
