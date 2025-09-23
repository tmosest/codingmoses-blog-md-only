---
title: LeetCode 2743. Count Substrings Without Repeating Character - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🔍 2743. Count Substrings Without Repeating Characters – “The Good, The Bad, and The Ugly”

> **Problem**  
> Given a lowercase string `s` (1 ≤ |s| ≤ 10⁵), count all *special* substrings – those that contain **no repeating character**.

> **Examples**  
> • `abcd` → 10  
> • `ooo` → 3  
> • `abab` → 7  

> **Goal**  
> Provide an **O(n)** solution in **Java, Python, and C++**, a concise **blog post** with SEO‑friendly keywords, and a quick‑look at the pitfalls (the “bad” and “ugly”) that often trip interviewers.

---

## 🚀 Why This Problem Is a Golden Interview Question

| **Aspect** | **Why It Matters** |
|-----------|--------------------|
| **Sliding Window** | Classic two‑pointer pattern; every candidate substring is inspected exactly once. |
| **Time Complexity** | O(n) is optimal; any slower approach is a red flag. |
| **Space Complexity** | O(1) (just an array of 26 ints) – demonstrates knowledge of space‑time trade‑offs. |
| **Edge Cases** | Single character, all same characters, alternating patterns. |

> **Keywords**: *substrings, sliding window, two pointers, string processing, interview problem, O(n) algorithm, Java, Python, C++*

---

## 📚 The Good: A Clean Sliding‑Window Implementation

### 1️⃣ Intuition

We expand a window `[left … right]` while the characters inside are unique.  
When a duplicate appears, we shrink the window from the left until the duplicate disappears.  
All substrings that end at `right` and start anywhere between `left` and `right` are valid, so we add `(right - left + 1)` to the answer.

### 2️⃣ Core Data Structures

| **Language** | **Structure** | **Size** |
|--------------|---------------|----------|
| Java, C++ | `int[26]` | Constant |
| Python | `list[26]` | Constant |

> The array indexes the count of each lowercase letter; 26 is a constant, so space is **O(1)**.

### 3️⃣ Pseudocode

```
count[26] = {0}
left = 0
answer = 0

for right in 0 … n-1:
    count[s[right]] += 1

    while count[s[right]] > 1:        # duplicate found
        count[s[left]] -= 1
        left += 1

    answer += right - left + 1

return answer
```

The loop runs **once** per character for both pointers – **O(n)** time.

---

## 🛠️ Code

Below are idiomatic solutions for the three requested languages.

### **Java**

```java
class Solution {
    public int numberOfSpecialSubstrings(String s) {
        int[] freq = new int[26];
        int left = 0, ans = 0;

        for (int right = 0; right < s.length(); right++) {
            freq[s.charAt(right) - 'a']++;

            while (freq[s.charAt(right) - 'a'] > 1) {
                freq[s.charAt(left) - 'a']--;
                left++;
            }

            ans += right - left + 1;
        }
        return ans;
    }
}
```

> **Complexities**  
> *Time*: **O(n)**  
> *Space*: **O(1)**

---

### **Python**

```python
class Solution:
    def numberOfSpecialSubstrings(self, s: str) -> int:
        freq = [0] * 26
        left = 0
        ans = 0

        for right, ch in enumerate(s):
            idx = ord(ch) - 97
            freq[idx] += 1

            while freq[idx] > 1:
                freq[ord(s[left]) - 97] -= 1
                left += 1

            ans += right - left + 1
        return ans
```

> **Complexities**  
> *Time*: **O(n)**  
> *Space*: **O(1)**

---

### **C++**

```cpp
class Solution {
public:
    int numberOfSpecialSubstrings(string s) {
        array<int, 26> freq{};
        int left = 0, ans = 0;

        for (int right = 0; right < (int)s.size(); ++right) {
            freq[s[right] - 'a']++;

            while (freq[s[right] - 'a'] > 1) {
                freq[s[left] - 'a']--;
                ++left;
            }

            ans += right - left + 1;
        }
        return ans;
    }
};
```

> **Complexities**  
> *Time*: **O(n)**  
> *Space*: **O(1)**

---

## ⚠️ The Bad: Common Pitfalls

| **Issue** | **Why It Fails** | **Fix** |
|-----------|------------------|---------|
| Using `String.contains()` or regex to check duplicates | O(n²) per check | Use the sliding‑window frequency array |
| Forgetting to reset `freq` when shrinking the window | Duplicate still counted | Decrement `freq[s[left]]` before moving `left` |
| Using `HashSet` for each right pointer (re‑inserting all chars) | O(n²) time | Keep a single set and remove only the leftmost char |

### Example

```java
for (int right = 0; right < n; right++) {
    Set<Character> seen = new HashSet<>();
    for (int i = right; i >= 0; i--) {
        if (!seen.add(s.charAt(i))) break;
        ans++;
    }
}
```

> **Complexity**: O(n²) – unacceptable for n = 10⁵.

---

## 🤡 The Ugly: Edge‑Case Traps

1. **All identical characters** (`"aaaaa"`).  
   *Expected*: count equals length of the string (only length‑1 substrings).  
   *Common bug*: Failing to shrink the window correctly leads to huge overcount.

2. **Maximum length string** (`n = 10⁵`).  
   *Memory limit*: A 2‑D array or list of lists is too heavy.  
   *Solution*: Keep the constant‑size frequency array.

3. **Mix of upper‑case and lower‑case** (though problem guarantees lowercase).  
   *Bug*: Index calculation `s[i] - 'a'` becomes negative.  
   *Check*: Validate input or use `Character.toLowerCase`.

4. **Non‑ASCII input** (rare).  
   *Fix*: Use Unicode code points or restrict to `a`‑`z`.

---

## 📈 SEO‑Optimized Summary

- **Title**: “2743 Count Substrings Without Repeating Characters – O(n) Sliding Window Guide (Java, Python, C++)”
- **Meta description**: “Master LeetCode 2743 with a clean O(n) sliding‑window solution. Learn the pitfalls, edge cases, and how to ace string interview problems in Java, Python, and C++.”
- **Primary keywords**: *LeetCode 2743, count substrings, sliding window, string without duplicates, interview algorithm, Java solution, Python solution, C++ solution*  
- **Secondary keywords**: *two pointers, hash map, O(n) time, constant space, edge cases, interview tips*

---

## 📅 Quick Study Plan (30 Minutes)

| Step | Time |
|------|------|
| 1. Read problem statement (5 min) |  
| 2. Think of sliding window, draft pseudocode (5 min) |  
| 3. Write Java code, run tests (10 min) |  
| 4. Translate to Python & C++ (5 min) |  
| 5. Review pitfalls, edge‑cases (5 min) |  

> **Result**: A polished, interview‑ready solution + a blog post that you can copy‑paste into LinkedIn, Medium, or a personal site.

---

## 🎯 Final Takeaway

- **Good**: One‑pass sliding window, constant space, clear math.  
- **Bad**: Quadratic approaches, wrong window maintenance.  
- **Ugly**: Edge‑case mishandling, over‑engineering.

> **If you can explain the above three columns and paste any of the code snippets, you’ll impress any hiring manager.**  

Happy coding, and good luck with your next interview! 🚀