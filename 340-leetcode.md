---
title: LeetCode 340. Longest Substring with At Most K Distinct Characters - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📌 340. Longest Substring with At Most K Distinct Characters  
**LeetCode** | **Difficulty**: Medium | **Tags**: Sliding Window, Hash Map

> **Problem**  
> Given a string `s` and an integer `k`, return the length of the longest substring of `s` that contains at most `k` distinct characters.

> **Examples**  
> 1. `s = "eceba"`, `k = 2` → **Output**: `3` (`"ece"`)  
> 2. `s = "aa"`, `k = 1` → **Output**: `2` (`"aa"`)

---

## ✅ Three Implementation Solutions

| Language | Code |
|----------|------|
| **Java** | [View](#java) |
| **Python** | [View](#python) |
| **C++** | [View](#c-plus-plus) |

> 👉 Each solution runs in **O(n)** time and uses **O(min(n, k))** space.

---

### 🧪 Java (Java 17)

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    public int lengthOfLongestSubstringKDistinct(String s, int k) {
        if (k == 0 || s.isEmpty()) return 0;

        int left = 0;
        int maxLen = 0;
        Map<Character, Integer> freq = new HashMap<>();

        for (int right = 0; right < s.length(); right++) {
            char r = s.charAt(right);
            freq.put(r, freq.getOrDefault(r, 0) + 1);

            // Shrink window until we have at most k distinct chars
            while (freq.size() > k) {
                char l = s.charAt(left++);
                freq.put(l, freq.get(l) - 1);
                if (freq.get(l) == 0) freq.remove(l);
            }

            maxLen = Math.max(maxLen, right - left + 1);
        }
        return maxLen;
    }
}
```

> **Explanation**  
> * `right` expands the window one character at a time.  
> * If the window contains more than `k` distinct chars, we move `left` forward, decrementing counts, until the constraint is satisfied.  
> * `maxLen` keeps track of the longest valid window seen so far.

---

### 🧪 Python 3

```python
class Solution:
    def lengthOfLongestSubstringKDistinct(self, s: str, k: int) -> int:
        if k == 0 or not s:
            return 0

        left = 0
        max_len = 0
        freq = {}

        for right, char in enumerate(s):
            freq[char] = freq.get(char, 0) + 1

            while len(freq) > k:
                left_char = s[left]
                freq[left_char] -= 1
                if freq[left_char] == 0:
                    del freq[left_char]
                left += 1

            max_len = max(max_len, right - left + 1)

        return max_len
```

> The Python version follows the exact same sliding‑window logic as Java, leveraging a dictionary for frequency counting.

---

### 🧪 C++ (C++17)

```cpp
#include <unordered_map>
#include <string>
#include <algorithm>

class Solution {
public:
    int lengthOfLongestSubstringKDistinct(std::string s, int k) {
        if (k == 0 || s.empty()) return 0;

        std::unordered_map<char, int> freq;
        int left = 0, maxLen = 0;

        for (int right = 0; right < static_cast<int>(s.size()); ++right) {
            freq[s[right]]++;

            while (freq.size() > static_cast<size_t>(k)) {
                char leftChar = s[left++];
                if (--freq[leftChar] == 0)
                    freq.erase(leftChar);
            }

            maxLen = std::max(maxLen, right - left + 1);
        }
        return maxLen;
    }
};
```

> Using `unordered_map` gives average‑case **O(1)** insert/delete operations, making the algorithm linear in practice.

---

## 📖 Blog Article: The Good, the Bad, and the Ugly of LeetCode 340

> **Title**: *“Mastering LeetCode 340 – Longest Substring with At Most K Distinct Characters: The Good, the Bad, and the Ugly”*  
> **Target Keywords**: LeetCode 340, Longest Substring with At Most K Distinct Characters, Sliding Window, Interview Coding Problem, Java Solution, Python Solution, C++ Solution, Algorithm Interview

---

### 1️⃣ Introduction

When recruiters ask “What’s the longest substring with at most k distinct characters?” they’re testing more than just your coding skill. They’re probing your understanding of **sliding window techniques**, **hash maps**, and **edge‑case handling**. In this article we’ll walk through:

- The algorithmic **good** – why the sliding window is the natural fit.  
- The algorithmic **bad** – pitfalls you might run into if you’re not careful.  
- The algorithmic **ugly** – how to make the solution clean, fast, and production‑ready.

---

### 2️⃣ The Problem in a Nutshell

> **Input**: `s` (string), `k` (int)  
> **Output**: Length of the longest contiguous substring that contains at most `k` distinct characters.  
> **Constraints**:  
> • `1 <= s.length <= 5 * 10⁴`  
> • `0 <= k <= 50`

---

### 3️⃣ The Good: Sliding Window + Hash Map

| Why it works | What we gain |
|--------------|--------------|
| The substring is **contiguous** → a window is a natural representation. | **Linear time**: each character enters and leaves the window at most once. |
| `k` is usually small (≤ 50) → storing character counts is cheap. | **O(min(n, k)) space**: we only keep counts for distinct characters inside the window. |

#### Pseudocode

```
left = 0
maxLen = 0
freq = empty map

for right in 0 .. n-1
    freq[s[right]] += 1

    while freq.size() > k
        freq[s[left]] -= 1
        if freq[s[left]] == 0
            remove s[left] from freq
        left += 1

    maxLen = max(maxLen, right - left + 1)

return maxLen
```

---

### 4️⃣ The Bad: Common Pitfalls

| Pitfall | Explanation | Fix |
|---------|-------------|-----|
| **Forgetting to handle `k = 0`** | The window can never contain any character; answer should be 0. | Add an early return: `if k == 0: return 0`. |
| **Off‑by‑one errors in window length** | Mixing `right - left` vs `right - left + 1`. | Use `right - left + 1` for inclusive window. |
| **Using a list instead of a hash map** | Counting frequencies naïvely leads to **O(n²)** in the worst case. | Use `HashMap`/`unordered_map`/dict. |
| **Not removing zero‑count keys** | The map grows with stale entries → `freq.size()` never shrinks correctly. | Remove key when count reaches 0. |
| **Assuming `k` > distinct chars in `s`** | The algorithm still works, but it’s an edge case to test. | Let the code handle it naturally; no special case needed. |

---

### 5️⃣ The Ugly: Making It Production‑Ready

1. **Avoid Repeated Lookups**  
   ```java
   char c = s.charAt(right);
   freq.put(c, freq.getOrDefault(c, 0) + 1);
   ```
   Instead of `s.charAt(right)` each time.

2. **Use Primitive Types When Possible**  
   In Java, use `int[] freq = new int[256]` if ASCII, for *O(1)* array lookups.  
   But if you need a generic solution for Unicode, a `HashMap` is safer.

3. **Inlining Helper Functions**  
   Keep the loop tight; inlining small helper methods reduces function call overhead.

4. **Thread‑Safe Variants**  
   If you ever run this in a multithreaded environment, use `ConcurrentHashMap` or synchronize the critical section.

5. **Unit Tests**  
   ```python
   assert Solution().lengthOfLongestSubstringKDistinct("eceba", 2) == 3
   assert Solution().lengthOfLongestSubstringKDistinct("aa", 1) == 2
   assert Solution().lengthOfLongestSubstringKDistinct("", 3) == 0
   assert Solution().lengthOfLongestSubstringKDistinct("abc", 0) == 0
   ```

---

### 6️⃣ Variations & Extensions

| Variation | How the solution changes |
|-----------|--------------------------|
| **Longest substring with exactly `k` distinct chars** | After window shrink, check `freq.size() == k` before updating `maxLen`. |
| **Infinite stream** | Use a `deque` to maintain the window; as new chars arrive, process the same sliding logic. |
| **Longest substring where each char appears at least `t` times** | Maintain counts and a separate counter for “satisfied” characters. |
| **Different character sets** | Replace `char` with `int` codes or use `unordered_map<string, int>` for substrings. |

---

### 7️⃣ Interview Tips

1. **Explain the invariant**: “The window always contains at most `k` distinct chars.”  
2. **Discuss the time complexity**: “Every character is added and removed at most once.”  
3. **Mention edge cases**: empty string, `k = 0`, `k` larger than number of distinct chars.  
4. **Talk about space optimization**: If the alphabet is fixed (ASCII), an array works.  
5. **Show the code step‑by‑step**: Don’t skip over the `while` loop that shrinks the window.

---

### 8️⃣ Final Thoughts

LeetCode 340 is a classic sliding‑window problem that tests your ability to maintain a dynamic invariant over a range of indices. The **good** lies in its simplicity and linear time, the **bad** is in careless implementation details, and the **ugly** becomes a production‑grade solution when you pay attention to small optimizations and robust testing.

Use the code snippets above as a reference for Java, Python, and C++ – all share the same core logic but differ in syntax and standard‑library utilities. Good luck cracking this problem and impressing your interviewers!

---

#### 🔗 References

- [LeetCode 340 – Longest Substring with At Most K Distinct Characters](https://leetcode.com/problems/longest-substring-with-at-most-k-distinct-characters/)  
- [Sliding Window Technique](https://leetcode.com/articles/sliding-window/)  

---

Happy coding! 🚀