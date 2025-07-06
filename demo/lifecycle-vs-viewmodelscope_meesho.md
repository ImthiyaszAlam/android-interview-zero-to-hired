
# Interview Question  
**What's the difference between `lifecycleScope` and `viewModelScope`? When should you use each?**

**Company:** Meesho  
**Role:** SDE-1 Android  
**Experience:** 1.5 years  

---

## Simplified Answer

`lifecycleScope` is tied to the screen (Activity or Fragment).  
`viewModelScope` is tied to the ViewModel.

Use `lifecycleScope` when the logic should stop when the screen is destroyed.  
Use `viewModelScope` when the logic should continue as long as the ViewModel lives — even during configuration changes like rotation.

---

## Explain Like I'm 5

- **ViewModel** = A class that holds UI data across screen changes  
- **Scope** = The lifecycle of a coroutine (how long it should run)  
- **lifecycleScope** = Cancels when screen is destroyed  
- **viewModelScope** = Cancels only when ViewModel is cleared  

---

## Common Mistake by Candidates

> “Both are used for launching coroutines. I just use `lifecycleScope` everywhere.”

That shows you don’t understand the real purpose of scopes. It could lead to:

- Memory leaks  
- Cancelled tasks during screen rotation  
- UI bugs and crashes  

---

## How to Answer in Interview

"`lifecycleScope` is tied to the UI lifecycle, so I use it for UI-specific tasks like animations or button clicks.  
`viewModelScope` survives screen rotations, so I use it for data loading or tasks that shouldn't restart unnecessarily.  
This keeps my app efficient, lifecycle-aware, and crash-free."

---

## Before vs After – Code Example

### Incorrect

```kotlin
class MyActivity : AppCompatActivity() {
    override fun onCreate(...) {
        lifecycleScope.launch {
            fetchUserProfile() // long-running task
        }
    }
}
````

* Problem: Activity dies on rotation → task is cancelled
* UI might not update or will show empty state

### Correct

```kotlin
class MyViewModel : ViewModel() {
    fun loadUser() {
        viewModelScope.launch {
            fetchUserProfile() // survives rotation
        }
    }
}
```

---

## Where This Goes Wrong in Real Projects

A developer loads data using `lifecycleScope` inside Activity.
User rotates screen → Activity restarts → coroutine cancels → no data shown.

Fix: Move data fetch to ViewModel using `viewModelScope`. Let ViewModel handle state. UI observes it.

---

## Red Flag Words to Avoid

* “I use lifecycleScope everywhere.”
* “They are kind of the same.”
* “I haven’t faced issues with GlobalScope.”

---

## Pro Tip

Never update UI directly from `viewModelScope`.
It has no reference to screen context.
Use LiveData or StateFlow to push updates back to UI safely.

---

## Cheatsheet – When to Use Which Scope

| Situation                          | Use This       |
| ---------------------------------- | -------------- |
| Update UI                          | lifecycleScope |
| Fetch data / business logic        | viewModelScope |
| Survive rotation / config change   | viewModelScope |
| Short-lived task (like button tap) | lifecycleScope |

---

## If You Don't Know This in Interview

Say this instead of bluffing:

> “I usually decide based on the task lifecycle, but would love your input if I’m off. I’m actively learning best practices.”

That shows honesty and coachability.

---

## TL;DR – 1-Min Summary

* `lifecycleScope`: tied to screen lifecycle — use for UI updates
* `viewModelScope`: tied to ViewModel — use for long-running or data logic
* Picking the wrong scope causes either leaks or cancelled tasks
* Don’t update UI from `viewModelScope` — use LiveData/StateFlow

