🧠 **Topic:**
Why using `GlobalScope` in Android is dangerous, and what to use instead.

🏢 **Asked in:**
**Flipkart**

---

## 🧩 Real Story from a Feature I Built

**Project:** Product listing screen with “Add to Cart” API in a shopping app.
**Team Pressure:** Deadline in 2 days.
**What I did (mistake):**

```kotlin
fun addToCart(productId: String) {
    GlobalScope.launch {
        val response = cartApi.add(productId)
        updateCartUI(response)
    }
}
```

It worked. The feature was live. Happy release.

**Then the bugs started.**

* App crashed when user left the screen quickly after clicking “Add to Cart.”
* Logs showed: `NullPointerException` in `updateCartUI()`.
* QA said: “Sometimes cart doesn’t update.”

---

## 🧠 What Went Wrong?

* I used **GlobalScope**, which **ignores lifecycle**.
* Even after user left the screen, the coroutine continued.
* It tried to update the UI of a screen that was already destroyed.
* Boom. **Crash.**

---

## 🛠️ Refactored Fix – Using viewModelScope

```kotlin
fun addToCart(productId: String) {
    viewModelScope.launch {
        val response = cartApi.add(productId)
        _cartStateFlow.value = response
    }
}
```

> Now if the user navigates away, coroutine auto-cancels.
> No crash. No leak. Smooth UI.

---

## 🧠 What I Learned

* **GlobalScope is not for UI logic. Ever.**
* It lives until the app dies. That's dangerous for Android lifecycles.
* Always tie coroutines to lifecycle-aware scopes like:

  * `viewModelScope` (in ViewModel)
  * `lifecycleScope` (in Activity/Fragment)

---

## 🎯 How to Say It in Interview

> “In my early projects, I made the mistake of using `GlobalScope` for API calls. It caused crashes when screens were closed too fast.
>
> Now I use `viewModelScope` or `lifecycleScope`, so coroutines are cancelled automatically when the screen is destroyed — preventing leaks and crashes. This follows structured concurrency in Android.”

---

## 🧪 Real Bugs You’ll Face If You Don’t Learn This

* `View is detached from window` crash when updating UI from dead coroutine
* API call continues even when the user navigates away
* Memory leak from long-running background jobs

---

## 🔥 Red Flag Phrases in Interview

> ❌ “I use GlobalScope because it’s global.”
> ❌ “It worked fine in my test device.”
> ❌ “I don’t think scopes matter much.”

---

## ✅ Words That Impress Interviewers

> ✅ “Structured concurrency”
> ✅ “Tied to lifecycle”
> ✅ “Cancels automatically”
> ✅ “Avoiding leaks and wasted processing”

---

## 📌 Quick Cheatsheet

| Scope          | Lifecycle-aware? | Use-case                           |
| -------------- | ---------------- | ---------------------------------- |
| GlobalScope    | ❌ No             | Only for app-wide tasks (rare)     |
| viewModelScope | ✅ Yes            | API calls, DB ops in ViewModel     |
| lifecycleScope | ✅ Yes            | UI-bound work in Activity/Fragment |

---

## ⏩ TL;DR – 1-Min Summary

* GlobalScope is not lifecycle-aware → can cause crashes or leaks.
* Always use `viewModelScope` or `lifecycleScope` for safe coroutines.
* Learn structured concurrency — interviewers love that phrase.
* If your coroutine touches UI, it **must** die with the screen.

ld I keep using **Project Diary**, or mix with another format (like Checklist or Mindmap)?
