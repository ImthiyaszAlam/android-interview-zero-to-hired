# Interview Question  
**Why does RecyclerView lag even after using DiffUtil? How can you fix it?**

**Company:** Flipkart  
**Role:** Android Engineer  
**Experience:** 2.0 years  

---

## Simplified Answer

Even with `DiffUtil`, your RecyclerView can lag if:

- You're inflating heavy layouts  
- You're loading too much data at once  
- You forgot to use `ListAdapter`  
- Your `onBindViewHolder()` does extra work  

`DiffUtil` is great — but it’s not magic.

---

## Explain Like I’m 5

- **RecyclerView** = A list that reuses item views to save memory  
- **DiffUtil** = A smart helper that updates only what changed in the list  
- **Lag** = When your scrolling feels slow or stutters  

If you write heavy or messy code, `DiffUtil` can’t save you.

---

## Common Mistake by Candidates

> “I used `DiffUtil`, so performance should be fine.”

Wrong.

You still need:

- Efficient `onBindViewHolder()`  
- Lightweight layouts  
- Async image loading  
- Paging for large datasets  

---

## How to Answer in Interview

"RecyclerView can lag if item views are complex, or too much work is done during binding.  

Even if I use `DiffUtil`, if I'm doing work like image decoding or calculations inside `onBindViewHolder()`, it causes frame drops.

To fix this, I:

- Use `ListAdapter` with DiffUtil  
- Load images using Glide or Coil  
- Simplify item layouts  
- Avoid nesting views deeply  
- Use Paging3 for large lists"

---

## Before vs After – Code Example

### ❌ Laggy Code (Heavy Work on Main Thread)

```kotlin
override fun onBindViewHolder(holder: MyViewHolder, position: Int) {
    val item = list[position]
    holder.title.text = item.title
    holder.image.setImageBitmap(getBitmap(item.url)) // 🔴 heavy work
}
````

### ✅ Optimized Code

```kotlin
override fun onBindViewHolder(holder: MyViewHolder, position: Int) {
    val item = getItem(position)
    holder.title.text = item.title
    Glide.with(holder.image).load(item.url).into(holder.image)
}
```

---

## Where This Goes Wrong in Real Projects

A developer loads 100+ items with images in a single API response.
They manually load images and do no recycling optimizations.

Result?

* RecyclerView scrolls with lag
* App drops frames
* Poor UX, crashes on low-end phones

Fix: Use Paging3 + Glide + `ListAdapter`.

---

## Red Flag Words to Avoid

* “I use DiffUtil, so it should be fast.”
* “I just call `notifyDataSetChanged()`.”
* “I don’t use any image library.”

---

## Pro Tip

Avoid calling `notifyDataSetChanged()` — it bypasses `DiffUtil`, rebinds every item, and destroys scroll performance.

Use:

* `submitList()` for `ListAdapter`
* `notifyItemChanged()` for specific items

---

## Cheatsheet – Quick Fix Table

| Problem          | Fix                             |
| ---------------- | ------------------------------- |
| Scroll lag       | Light layouts + clean binding   |
| List flickers    | Use `ListAdapter` with DiffUtil |
| Image load delay | Use Glide/Coil with caching     |
| Memory overload  | Use Paging3 for large datasets  |

---

## If You Don't Know This in Interview

Say:

> "I know DiffUtil helps reduce work, but scrolling can still lag if I write heavy bind logic. I’d also optimize layout, image loading, and list size."

This shows you're aware of real-world performance factors.

---

## TL;DR – 1-Min Summary

* DiffUtil helps detect changes, but won’t fix lag from poor code
* Avoid doing heavy work inside `onBindViewHolder()`
* Use Glide or Coil for image loading
* Use Paging3 for large datasets
* Avoid `notifyDataSetChanged()` — it's a performance killer

---

**Part of the Android Interview Kit built from 300+ real interviews with engineers at Flipkart, Swiggy, Zomato, Meesho, Dream11 & more.**

📥 [Follow on Medium](https://medium.com/@developerimthiyas)
🤝 [Connect on LinkedIn](https://linkedin.com/in/imthiyasalam)

