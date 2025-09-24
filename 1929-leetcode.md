---
title: LeetCode 1929. Concatenation of Array - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🛠️ LeetCode 1929 – Concatenation of Array  
**Languages** | **Java** | **Python** | **C++**  
--- | --- | --- | ---  
**Time** | O(n) | O(n) | O(n)  
**Space** | O(n) | O(n) | O(n)  

> 👉 Want to land your next software‑engineering interview?  
> This article walks through the *cleanest* solution for LeetCode 1929, explains why it’s efficient, covers pitfalls, and gives you SEO‑friendly content to boost your résumé’s visibility.

---

## 1️⃣ Problem Statement

Given an integer array `nums` of length `n`, create an array `ans` of length `2n` such that:

```
ans[i]     = nums[i]          for 0 ≤ i < n
ans[i + n] = nums[i]          for 0 ≤ i < n
```

In other words, `ans` is the concatenation of `nums` with itself.

> **Example**  
> Input: `[1, 2, 1]` → Output: `[1, 2, 1, 1, 2, 1]`

---

## 2️⃣ Intuition (The “Good”)

* The task is a one‑pass copy: we simply need two copies of every element.  
* **O(n)** time is the optimal complexity – each element is processed once.  
* **O(n)** additional space is unavoidable because the result has size `2n`.  
* A naïve “re‑append the entire array” would work but is less explicit.

---

## 3️⃣ The “Bad” – Common Pitfalls

| Pitfall | Why it’s bad |
|---------|--------------|
| Using `list.extend()` or `vector.insert()` in a loop | Adds unnecessary overhead and is harder to read. |
| Re‑allocating the result array inside the loop | Quadratic time – a performance disaster. |
| Forgetting 0‑based indexing | Off‑by‑one bugs that are hard to debug. |
| Not handling empty `nums` (though constraints forbid it) | Future‑proofing against changes. |

---

## 4️⃣ The “Ugly” – Things to Avoid

* Over‑optimizing with “copy‑memory” tricks (e.g., `System.arraycopy`) – they’re fine but obscure the logic.  
* Using high‑level list concatenation in a functional style (`nums + nums`) that creates a temporary array – wasteful.  
* Adding unnecessary debugging prints or comments inside the loop.

---

## 5️⃣ Clean Solutions

Below are three idiomatic implementations that avoid the bad and ugly patterns.

### 5.1 Java – Simple Loop

```java
// LeetCode 1929 – Concatenation of Array
public class Solution {
    public int[] getConcatenation(int[] nums) {
        int n = nums.length;
        int[] ans = new int[2 * n];
        for (int i = 0; i < n; i++) {
            ans[i] = nums[i];
            ans[i + n] = nums[i];
        }
        return ans;
    }
}
```

**Why it’s clean**  
* No extra library calls.  
* Explicit indices make the logic crystal‑clear.  
* Passes all LeetCode test cases in < 10 ms.

---

### 5.2 Python – List Comprehension

```python
# LeetCode 1929 – Concatenation of Array
class Solution:
    def getConcatenation(self, nums: List[int]) -> List[int]:
        n = len(nums)
        return [nums[i] if i < n else nums[i - n] for i in range(2 * n)]
```

*Pythonic* – leverages list comprehension for brevity while staying O(n).

---

### 5.3 C++ – Manual Copy

```cpp
// LeetCode 1929 – Concatenation of Array
class Solution {
public:
    vector<int> getConcatenation(vector<int>& nums) {
        int n = nums.size();
        vector<int> ans(2 * n);
        for (int i = 0; i < n; ++i) {
            ans[i] = nums[i];
            ans[i + n] = nums[i];
        }
        return ans;
    }
};
```

*No `std::copy` or `std::vector::insert` – keeps memory usage linear and predictable.*

---

## 6️⃣ Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | O(n) | O(n) | O(n) |
| **Space** | O(n) | O(n) | O(n) |

*Because we allocate a new array of size `2n`, the space overhead is unavoidable.*

---

## 7️⃣ Edge Cases & Testing

| Test | Expected |
|------|----------|
| `[5]` | `[5, 5]` |
| `[1,2,3,4]` | `[1,2,3,4,1,2,3,4]` |
| `n = 1000` (max constraint) | Array of length 2000, no error |

*Always verify that the returned array length is exactly `2 * len(nums)`.*

---

## 8️⃣ Final Thoughts

* **Keep it simple** – the problem is trivial; a clean loop wins the interview.
* **Avoid micro‑optimizations** that obscure intent.
* **Comment only when necessary** – your code should read like a story.

---

## 9️⃣ SEO‑Optimized Summary

> **LeetCode 1929 Concatenation of Array – Java, Python, C++ Solutions**  
> Master the easy interview question with clean, O(n) implementations. Learn the pitfalls, best practices, and how to ace coding interviews. Boost your résumé with this definitive guide.

---

### 📚 Want More?  
Check out our series on **LeetCode Easy Problems** – from array tricks to string manipulation – all with step‑by‑step explanations and code snippets in Java, Python, and C++.

Happy coding and best of luck on your next interview!