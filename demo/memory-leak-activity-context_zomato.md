

🧠 **Topic:**
Why using `Activity Context` in adapters or utility classes can crash your app or cause memory leaks.

🏢 **Asked in:**
**Zomato**

---

## 🎭 *Real Bug That Happened at a Startup*

There was this junior dev.
Fresh out of college. Built a beautiful food-ordering app.
Smooth UI, crispy XML. Everyone loved the animations.

But then…

> Users started reporting:
>
> * "App slows down after some time."
> * "Why is my phone heating up?"
> * "I closed the app, but battery still draining."

What happened?

He had written this:

```kotlin
class FoodAdapter(val context: Context) : RecyclerView.Adapter<FoodAdapter.ViewHolder>() { 
   // used context to show Toasts, inflate views, etc.
}
```

**And guess what he passed in?**

```kotlin
val adapter = FoodAdapter(this) // inside Activity
```

He thought he was being smart. But he passed `Activity context` — and `RecyclerView` lived longer than the Activity.
**Boom: Memory leak.**

---

## 🧠 What Actually Went Wrong?

* He passed **Activity Context** to something that outlived the Activity.
* Android couldn’t garbage-collect the Activity because `FoodAdapter` was still holding a reference.
* Over time, memory piled up. App slowed. Battery drained. Eventually: **OOM crash.**

---

## ✅ The Expert Fix (🔥 Short and Clean)

```kotlin
val adapter = FoodAdapter(applicationContext) // ✅ Safer option
```

OR BETTER — don’t even pass context:

```kotlin
class FoodAdapter : RecyclerView.Adapter<...>() {
    // Use parent.context inside onCreateViewHolder instead
}
```

---

## 💣 Classic Mistakes in Interviews

> ❌ "I always pass `this` as context."
> ❌ "I use context to load Glide, so I keep it in the adapter."
> ❌ "I don't know the difference between Application and Activity context."

---

## ✅ How You Should Say It in the Interview

> “Context is powerful but dangerous in Android.
>
> I avoid passing `Activity Context` to long-living objects like adapters, singletons, or utils.
> If I **must** pass it, I prefer `Application Context`, or refactor to not require it at all — like using `parent.context` inside view holders.”

---

## 🎯 Pro Tip – To Impress

> If you're showing a `Dialog`, use `Activity Context`.
> If you're inflating layouts or using Glide — use `Application Context`, or better, **don't store it at all**.
> For lifecycle-safe background work, avoid both and use ViewModel + Repository pattern.

---

## ❗ In the Wild: Where This Still Breaks Apps

* Toasts inside Adapters using Activity Context
* View inflations in singletons
* Glide/Coil using leaked Activities
* Dagger/Singletons holding Activity-scoped context

---

## 🛠️ Quiz – Spot the Leak:

```kotlin
object AppUtils {
    lateinit var context: Context

    fun init(ctx: Context) {
        context = ctx
    }
}
```

🔥 *Is this okay?*
**Nope. You’ve just created a global leak. If someone passes Activity context to this, the Activity will never die.**

---

## ⏩ TL;DR – 1-Min Summary

* **Never store Activity Context** in global/static places.
* Prefer **Application Context** for utils and adapters.
* Use `parent.context` in Adapters.
* If you see `this` passed in blindly — smell the leak.
* This is a **real interview filter question**. Nail it and you're through.

