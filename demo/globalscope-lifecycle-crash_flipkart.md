
# Interview Question  
**Why using `GlobalScope` in Android is dangerous, and what to use instead**

**Company:** Flipkart  
**Role:** Android Developer  
**Experience:** 1.5+ years

---

## Real Story from a Feature I Built

**Project:** Product listing screen with “Add to Cart” API in a shopping app  
**Context:** Deadline in 2 days. Quick implementation needed.

### What I did (mistake):

```kotlin
fun addToCart(productId: String) {
    GlobalScope.launch {
        val response = cartApi.add(productId)
        updateCartUI(response)
    }
}
````

It worked. The feature was live. The release went through.

**Then the bugs started:**

* App crashed when user left the screen quickly after clicking “Add to Cart”
* Stacktrace showed: `NullPointerException` in `updateCartUI()`
* QA reported: “Sometimes cart doesn’t update”

---

## What Went Wrong

* GlobalScope ignores the Android lifecycle
* Coroutine continued even after the user navigated away
* It tried to update a destroyed screen → crash
* Uncancelled work = memory leaks + wasted resources

---

## Refactored Fix – Using `viewModelScope`

```kotlin
fun addToCart(productId: String) {
    viewModelScope.launch {
        val response = cartApi.add(productId)
        _cartStateFlow.value = response
    }
}
```

Now, if the user leaves the screen:

* Coroutine is cancelled automatically
* UI is never updated from a dead screen
* App remains smooth and crash-free

---

## What I Learned

* GlobalScope is not safe for UI logic
* It lives as long as the app — not tied to screen lifecycle
* Always use lifecycle-aware scopes:

  * `viewModelScope` for ViewModel
  * `lifecycleScope` for Activity/Fragment

---

## How to Say It in Interview

“In my earlier projects, I used `GlobalScope` to launch API calls. But that caused crashes when users left the screen early.

Now I tie every coroutine to `viewModelScope` or `lifecycleScope`. This ensures structured concurrency — coroutines cancel automatically if the UI is gone. It prevents leaks, crashes, and aligns with Android’s lifecycle.”

---

## Where This Goes Wrong in Real Projects

* Crashes with `View is detached from window` error
* API call continues when user is on a different screen
* Memory leak from jobs that never got cancelled

---

## Red Flag Phrases in Interview

* “I use GlobalScope because it’s global”
* “It works fine on my device”
* “I don’t think lifecycle scope matters much”

---

## Phrases That Impress Interviewers

* “Structured concurrency”
* “Scoped to lifecycle”
* “Automatic cancellation”
* “Leak prevention through scoped coroutines”

---

## Quick Cheatsheet

| Scope          | Lifecycle-aware | When to Use                          |
| -------------- | --------------- | ------------------------------------ |
| GlobalScope    | No              | Rare app-wide tasks only             |
| viewModelScope | Yes             | ViewModel API/DB tasks               |
| lifecycleScope | Yes             | UI-related work in Activity/Fragment |

---

## TL;DR – 1-Min Summary

* GlobalScope ignores lifecycle → leads to crashes
* Always prefer `viewModelScope` or `lifecycleScope`
* Use structured concurrency to match Android lifecycle
* Coroutines touching UI must die with the screen


