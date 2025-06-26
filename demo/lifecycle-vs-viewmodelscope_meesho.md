# 🧠 Interview Question

**What's the difference between `lifecycleScope` and `viewModelScope`? When should you use each?**

🏢 **Asked in:** Meesho (SDE-1 Android Round)

---

## 🧩 Simplified Answer

`lifecycleScope` is tied to the screen (Activity or Fragment).
`viewModelScope` is tied to the ViewModel.

Use `lifecycleScope` when the logic should stop when the screen is destroyed.
Use `viewModelScope` when the logic should continue as long as the ViewModel lives (even during config changes).

---

## 📚 Explain Like I'm 5

* **ViewModel** = A class that holds UI data.
* **Scope** = The lifecycle of a coroutine (how long it should run).
* **lifecycleScope** = Dies when Activity/Fragment is destroyed.
* **viewModelScope** = Dies only when ViewModel is cleared.

---

## 🚫 Common Mistake by Candidates

“Both are used for launching coroutines. I just use `lifecycleScope` everywhere.”

✅ Interviewer immediately knows: you don’t understand the **scope** lifecycle, and your app might be leaking memory or cancelling things too early.

---

## ✅ How to Answer in Interview

"`lifecycleScope` is tied to the UI lifecycle — so I use it when the task is UI-specific and should stop if the user leaves the screen.

`viewModelScope` survives config changes like screen rotation. So I use it for long-running tasks like fetching data or storing state.

This helps avoid memory leaks and ensures my coroutines live as long as they should — no more, no less."

---

## 🛠️ Before vs After (Code Example)

### ❌ Wrong:

```kotlin
class MyActivity : AppCompatActivity() {
    override fun onCreate(...) {
        lifecycleScope.launch {
            fetchUserProfile() // long task
        }
    }
}
```

* 🔴 Problem: If user rotates the screen, Activity dies → task is cancelled.

### ✅ Right:

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

## 🧪 Where This Goes Wrong in Real Projects

A dev loads important data using `lifecycleScope` inside an Activity.
The user rotates the phone.

The data loading cancels.
The new screen shows **blank UI** or inconsistent state.

The fix? Move that logic to `viewModelScope`.

---

## 💣 Red Flag Words to Avoid

* “I use lifecycleScope for everything.”
* “They are basically the same.”

---

## 🎯 Pro Tip

**Don’t access UI elements from `viewModelScope`!**
It has no reference to Activity or Fragment.
Do the data work → then update UI via LiveData or StateFlow.

---

## 📌 Mini Cheatsheet

| Situation                           | Use This       |
| ----------------------------------- | -------------- |
| Update UI                           | lifecycleScope |
| Fetch data / business logic         | viewModelScope |
| Need to survive rotation            | viewModelScope |
| Short-lived job (like button click) | lifecycleScope |

---

## 🔍 If You Don't Know This in Interview

Say:

> "I usually use both depending on context, but I’d love to explain my understanding and get feedback if I’m wrong."

This shows coachability and honesty, which is better than bluffing.

---

## ⏩ TL;DR – 1-Min Summary

* `lifecycleScope`: dies with screen
* `viewModelScope`: survives screen rotation, tied to ViewModel
* Use `lifecycleScope` for UI tasks, `viewModelScope` for logic/data
* Misusing scopes can cancel important tasks or cause memory leaks

---

📘 Want 49 more interview questions like this?
📥 [Follow me on Medium](https://medium.com/@developerimthiyas)
🤝 [Connect on LinkedIn](https://www.linkedin.com/in/imthiyasalam)
