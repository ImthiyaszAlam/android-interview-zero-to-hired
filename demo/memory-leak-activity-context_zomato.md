# Interview Question  
**Why using `Activity Context` in adapters or utility classes can crash your app or cause memory leaks**

**Company:** Zomato  
**Role:** Android Developer  
**Experience:** 1.5 years  

---

## Real Bug That Happened at a Startup

There was this junior developer.  
Fresh out of college. Built a beautiful food-ordering app.  
Smooth UI, crispy XML. Everyone loved the animations.

But then…

> Users started reporting:
>
> - "App slows down after some time."
> - "Why is my phone heating up?"
> - "I closed the app, but battery still draining."

What happened?

He had written this:

```kotlin
class FoodAdapter(val context: Context) : RecyclerView.Adapter<FoodAdapter.ViewHolder>() { 
    // used context to show Toasts, inflate views, etc.
}
````

**And guess what he passed in?**

```kotlin
val adapter = FoodAdapter(this) // inside Activity
```

He passed the `Activity context`.
The RecyclerView adapter outlived the Activity.
**Result: Memory leak.**

---

## What Actually Went Wrong?

* Adapter **held onto Activity context** even after screen was destroyed
* Activity couldn’t be garbage collected
* Memory piled up → App slowed → Phone overheated → Crash (OOM)

---

## The Expert Fix – Short and Clean

Instead of passing context blindly:

```kotlin
val adapter = FoodAdapter(applicationContext) // ✅ Safer option
```

**Or better — don’t pass it at all.**

```kotlin
class FoodAdapter : RecyclerView.Adapter<...>() {
    // use parent.context in onCreateViewHolder()
}
```

---

## Common Mistakes in Interviews

> ❌ "I always pass `this` as context."
> ❌ "I use Glide inside adapter, so I keep context there."
> ❌ "I didn’t know there's a difference between application and activity context."

---

## How You Should Say It in the Interview

> “Context is powerful but dangerous in Android.
>
> I avoid storing `Activity Context` in long-living objects like adapters, utils, or singletons.
> If needed, I pass `Application Context` or restructure the code to not require it — like using `parent.context` inside `onCreateViewHolder()`.”

---

## Pro Tip – To Impress

* Use `Activity Context` only for things like **Dialog**, **AlertDialog**, or **permission requests**
* Use `Application Context` for non-UI things: Glide, inflating views, Room, WorkManager
* In MVVM: keep context out of `ViewModel` and use repository pattern instead

---

## In the Wild – Where This Still Breaks Apps

| Problem Area                      | What's Wrong                        |
| --------------------------------- | ----------------------------------- |
| Toasts inside Adapter             | Might crash when screen is gone     |
| View inflations in Singleton      | Holds onto Activity context forever |
| Glide with leaked context         | Memory leak, UI lag                 |
| Global utils holding Activity ctx | Prevents GC, causes OOM             |

---

## Code Quiz – Spot the Leak

```kotlin
object AppUtils {
    lateinit var context: Context

    fun init(ctx: Context) {
        context = ctx
    }
}
```

**Is this okay?**
No. If you pass `Activity context` here, the entire screen will leak.
**Always pass `applicationContext`** in such static/global cases.

---

## TL;DR – 1-Min Summary

* Don’t store `Activity Context` in adapters, utils, or singletons
* Prefer `Application Context` when lifecycle doesn’t matter
* Use `parent.context` inside Adapters for safe inflation
* Always think: “Will this object live longer than the screen?”
* This is a real interview filter question. Nail it and stand out.


