# 🧠 Interview Question

**Why does RecyclerView lag even after using DiffUtil? How can you fix it?**

🏢 **Asked in:** Flipkart (Android Engineer Round)

---

## 🧩 Simplified Answer

Even with `DiffUtil`, your RecyclerView can lag if:

* You're inflating heavy layouts
* You're loading too much data at once
* You forgot to use `ListAdapter`
* Your `onBindViewHolder()` does extra work

`DiffUtil` is great — but it’s not magic.

---

## 📚 Explain Like I’m 5

* **RecyclerView** = A list that reuses item views to avoid memory waste.
* **DiffUtil** = A tool to smartly find changes in your list and update only what’s changed.
* **Lag** = When scrolling stutters, freezes, or delays.

Just using DiffUtil doesn't fix bad design or poor code.

---

## 🚫 Common Mistake by Candidates

“I used `DiffUtil`, so performance should be fine.”

Wrong.
You also need:

* Efficient `onBindViewHolder()`
* Light item layouts
* Async image loading
* Proper paging if list is large

---

## ✅ How to Answer in Interview

"RecyclerView can lag due to heavy layouts, unoptimized `onBindViewHolder()`, or large data sets.

Even with `DiffUtil`, if I do too much work inside `onBindViewHolder()` — like loading images or doing calculations — scrolling will lag.

I fix this by using:

* `ListAdapter` (which uses DiffUtil internally)
* Image loading libs like Glide or Coil
* Keeping item layout minimal
* Avoiding nested views or complex hierarchies
* Using Paging3 for large datasets"

---

## 🛠️ Before vs After (Code Example)

### ❌ Laggy Code:

```kotlin
override fun onBindViewHolder(holder: MyViewHolder, position: Int) {
    val item = list[position]
    holder.title.text = item.title
    holder.image.setImageBitmap(getBitmap(item.url)) // heavy work on main thread
}
```

### ✅ Smooth Code:

```kotlin
override fun onBindViewHolder(holder: MyViewHolder, position: Int) {
    val item = getItem(position)
    holder.title.text = item.title
    Glide.with(holder.image).load(item.url).into(holder.image)
}
```

---

## 🧪 Where This Goes Wrong in Real Projects

A dev loads 100+ items with images in a single API call.
They bind images manually without caching.
Result? RecyclerView scrolls like a broken elevator.

Fix: use Paging3 + Glide + proper ViewHolder recycling.

---

## 💣 Red Flag Words to Avoid

* “DiffUtil handles everything.”
* “I just update the whole list.”

---

## 🎯 Pro Tip

Avoid calling `notifyDataSetChanged()` unless absolutely necessary.
It skips DiffUtil and refreshes the entire list, killing performance.

---

## 📌 Mini Cheatsheet

| Problem         | Fix                               |
| --------------- | --------------------------------- |
| Laggy scrolling | Light layouts + efficient binding |
| Full refresh    | Use DiffUtil or ListAdapter       |
| Heavy data      | Use Paging3                       |
| Image lag       | Use Glide/Coil with caching       |

---

## 🔍 If You Don't Know This in Interview

Say:

> "I know DiffUtil is not a full solution. I’d focus on optimizing layout and binding, and maybe use Paging or async image loading."

Shows awareness, even if you haven’t implemented everything yet.

---

## ⏩ TL;DR – 1-Min Summary

* DiffUtil helps but isn’t enough
* Keep `onBindViewHolder()` super lightweight
* Use image libraries like Glide or Coil
* Use Paging3 for large datasets
* Never trust `notifyDataSetChanged()` blindly

---

📘 Want 48 more interview Q\&As like this?
📥 [Follow me on Medium](https://medium.com/@developerimthiyas)
🤝 [Connect on LinkedIn](https://www.linkedin.com/in/imthiyasalam)
