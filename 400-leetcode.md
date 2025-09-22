---
title: LeetCode 400. Nth Digit - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 Mastering LeetCode 400 – “Nth Digit”  
*(Java | Python | C++ Solutions + SEO‑Optimized Blog Post to land that tech job)*  

---

## 1️⃣ Problem Recap  
**LeetCode 400 – Nth Digit**  
> **Medium** | `int findNthDigit(int n)`  

> Given an integer `n`, return the nth digit of the infinite integer sequence  
> `1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, …`.  
> `1 <= n <= 2³¹‑1`.

**Examples**

| n | Output | Explanation |
|---|--------|-------------|
| 3 | 3 | The third digit is `3`. |
| 11 | 0 | The 11th digit comes from the number `10` → digit `0`. |

---

## 2️⃣ Why This Problem Rocks for Interviews  

| ✅ | Value |
|---|-------|
| ✔️ **Core Concepts** | Positional math, digit counting, simple loops. |
| ✔️ **Time Complexity** | `O(log10 n)` – very fast. |
| ✔️ **Space Complexity** | `O(1)` – no extra data structures. |
| ✔️ **Tricky Edge Cases** | Numbers with many digits (e.g., 100‑digit numbers) and integer overflow. |
| ✔️ **Common Interview Themes** | “Find the nth element in an implicit sequence”, “Avoid generating the whole sequence”, “Use math instead of simulation”. |

---

## 3️⃣ Three Clean Solutions  

> All three solutions follow the same mathematical idea:  
> 1. Find the **digit length** (1‑digit numbers, 2‑digit numbers, …) that contains the `n`th digit.  
> 2. Compute the exact number that holds the digit.  
> 3. Extract the digit via string conversion or arithmetic.  

### 3.1 Java 17 (O(1) space, O(log n) time)

```java
class Solution {
    public int findNthDigit(int n) {
        // 1. Find the digit length block
        long digitLen = 1;          // current block's digit length
        long count = 9;             // how many numbers in this block
        long start = 1;             // first number in this block

        while (n > digitLen * count) {
            n -= digitLen * count;  // skip the whole block
            digitLen++;
            start *= 10;            // move to next block (10, 100, 1000 …)
            count *= 10;            // 9, 90, 900 …
        }

        // 2. Locate the exact number
        long number = start + (n - 1) / digitLen;

        // 3. Grab the digit
        return String.valueOf(number).charAt((int)((n - 1) % digitLen)) - '0';
    }
}
```

> **Why `long`?**  
> `n` can be up to `2³¹‑1`. During the loop we multiply by `digitLen`, so using `long` prevents overflow.

---

### 3.2 Python 3 (Elegant and idiomatic)

```python
class Solution:
    def findNthDigit(self, n: int) -> int:
        digit_len = 1          # current block's digit length
        count = 9              # how many numbers in this block
        start = 1              # first number in this block

        # Find the block that contains the nth digit
        while n > digit_len * count:
            n -= digit_len * count
            digit_len += 1
            start *= 10
            count *= 10

        # Exact number holding the digit
        number = start + (n - 1) // digit_len

        # Digit position inside the number
        index = (n - 1) % digit_len
        return int(str(number)[index])
```

> Python’s arbitrary‑precision integers mean we don’t have to worry about overflow, but the `long`‑style loop keeps it `O(log n)`.

---

### 3.3 C++17 (Performance‑oriented, no string overhead)

```cpp
class Solution {
public:
    int findNthDigit(int n) {
        long long digitLen = 1;      // block's digit length
        long long count = 9;         // numbers in this block
        long long start = 1;         // first number in this block

        while (static_cast<long long>(n) > digitLen * count) {
            n -= digitLen * count;   // skip block
            digitLen++;
            start *= 10;
            count *= 10;
        }

        long long number = start + (n - 1) / digitLen;
        int index = (n - 1) % digitLen;

        // Convert to string only once
        std::string s = std::to_string(number);
        return s[index] - '0';
    }
};
```

> **Why `long long`?** Same overflow reasoning as Java.  
> If you want to avoid `to_string`, you can strip digits with a simple division loop.

---

## 4️⃣ Complexity & “Job‑Interview” Takeaways  

| ✅ | Metric | Result |
|---|--------|--------|
| Time | `O(log10 n)` | ~< 10 iterations even for the largest 32‑bit input. |
| Space | `O(1)` | Only a handful of primitive variables. |
| **Pitfall** | Integer overflow – use `long`/`long long`. |
| **Common Interview Twist** | “What if you had to return the whole number instead of a single digit?” (easy to extend). |

---

## 5️⃣ “The Good, The Bad, and The Ugly” – Interview Insights  

### 5️⃣1 The Good  
* **Fast math trick** – you’ll impress interviewers by skipping huge portions of the sequence.  
* **Scalable** – works for 10‑digit, 100‑digit numbers with just one loop.  
* **Portability** – same logic fits any language (Java, Python, C++, Go, Rust).  

### 5️⃣2 The Bad  
* **Naïve simulation** (e.g., concatenating strings until `n` is reached) blows up quickly (`O(n)` time, `O(n)` space).  
* Forgetting to handle **overflow** can lead to wrong answers on large inputs.  
* Some candidates over‑engineer: build a hash map of digit blocks or pre‑compute powers of ten.  
  *This is unnecessary* – a simple loop is enough.  

### 5️⃣3 The Ugly  
* **Edge‑case confusion**:  
  * `n == 9` → digit is `9` (first block).  
  * After subtracting a whole block, `n` might become exactly a multiple of `digitLen`.  
    *Example:* `n = 180` (the last digit of the 2‑digit block) → digit `9`.  
* **Integer division vs. modulo** – off‑by‑one errors are common.  
* **String vs. arithmetic extraction** – both work, but string conversion is more readable in Java/Python, while pure arithmetic is faster in C++.  

---

## 6️⃣ Interview “Gold‑Dust” Tips  

| Tip | Why it Matters |
|-----|----------------|
| **Explain your math** | Interviewers love to see you walk through the positional reasoning before writing code. |
| **Mention overflow** | Shows awareness of language limits (`int` → `long`/`long long`). |
| **Talk about `O(log n)`** | Highlights algorithmic efficiency. |
| **Show a quick test harness** | If asked on the spot, a small script to verify edge cases boosts confidence. |
| **Practice edge cases** | 1, 9, 10, 11, 19, 20, 100, 101, 110, 999, 1000, 10000, … |

> **“If you can solve this problem, you can solve any implicit sequence interview question.”**

---

## 7️⃣ Related Questions & Variants  

| # | Problem | What it Teaches |
|---|---------|-----------------|
| 200 | *Number of Islands* | Graph traversal, BFS/DFS. |
| 234 | *Palindrome Linked List* | Two‑pointer technique, O(1) space. |
| 541 | *Reverse String in Blocks* | Sliding windows, string manipulation. |
| 1309 | *Decompress Run Length Encoded List* | Array manipulation, efficient decoding. |

> If you’re prepping for **Google, Amazon, Facebook, Microsoft**, you’ll likely get variations that ask for the *nth number* instead of the *nth digit*, or the *nth prime* in an implicit sequence.

---

## 8️⃣ How to Use This Blog Post to Boost Your Tech‑Job Resume  

1. **Keyword‑Rich Title** – “Nth Digit LeetCode 400 Java Solution”  
2. **Meta Description** – “Learn the fastest Java, Python, and C++ solutions for LeetCode 400 – Nth Digit. Boost your interview skills and land your dream tech job.”  
3. **Header Tags** – `<h1>`, `<h2>`, `<h3>` with target keywords.  
4. **Internal Links** – link to other algorithm blogs (e.g., “Two Sum”, “Binary Tree Inorder Traversal”).  
5. **External Links** – reference the official LeetCode page, the GitHub repo, and interview‑prep blogs.  

---

## 9️⃣ Takeaway – “The Good, The Bad, and The Ugly”

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **The Good** | O(log n) time, O(1) space, no extra memory, pure math. | – | – |
| **The Bad** | Off‑by‑one mistakes, forgetting overflow, wrong block calculation. | – | – |
| **The Ugly** | Someone might try to build the entire string (`"123456789101112..."`) until `n`, which is *massively* inefficient and can overflow memory. | Avoid this naive simulation. | Use math first. |

> **Bottom line:** The “Nth Digit” problem is a *must‑know* for any software‑engineering interview. Master the math, write clean code in your language of choice, and you’ll impress recruiters with both technical skill and interview‑ready thinking.

---

## 📚 Final Word – Land That Dream Job  

*Your interview stack now contains:*

- **Java** – industry‑standard for back‑end and Android roles.  
- **Python** – favored for data‑science, scripting, and quick prototyping.  
- **C++** – essential for systems programming, gaming, and high‑performance computing.  

Show recruiters that you can solve problems efficiently **without brute force** and you’ll stand out among the millions of candidates. Good luck, and happy coding! 🚀

--- 

### 👨‍💻 Want a deeper dive?  
Check out my full GitHub repo:  
[`https://github.com/yourhandle/leetcode-400-nth-digit`](#)  

Feel free to **share** this article on LinkedIn, Medium, or any developer community to increase your visibility. Good luck on your next interview!