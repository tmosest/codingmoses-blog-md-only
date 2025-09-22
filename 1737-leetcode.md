---
title: LeetCode 1737. Change Minimum Characters to Satisfy One of Three Conditions - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## LeetCode 1737 – *Change Minimum Characters to Satisfy One of Three Conditions*  
### A Complete, SEO‑Optimized Guide (Java / Python / C++)

> **Keywords:** LeetCode 1737, Change Minimum Characters, coding interview, algorithm, prefix sum, frequency array, Java, Python, C++, job interview, medium problem, string manipulation

---

### 1️⃣ Problem Recap

You are given two lowercase strings **`a`** and **`b`**.

In one operation you may change any single character in either string to **any** other lowercase letter.  
You need to achieve **at least one** of the following three conditions using the minimum number of operations:

| Condition | Description |
|-----------|-------------|
| 1 | Every letter in **`a`** is **strictly less** than every letter in **`b`** (alphabetical order). |
| 2 | Every letter in **`b`** is **strictly less** than every letter in **`a`**. |
| 3 | Both strings consist of **only one distinct letter** each (they may be different). |

Return the minimum number of operations required.

> **Example**  
> `a = "aba"`, `b = "caa"` → answer `2`.

---

### 2️⃣ Why Brute Force Fails

A naïve solution would try every possible pair of target strings and count changes – this is **O(26^(|a|+|b|))** in the worst case.  
With string lengths up to **10⁵**, this is impossible.  
We need an **O(n)** algorithm.

---

### 3️⃣ The Efficient Idea

1. **Count letter frequencies** in each string.  
   For each of the 26 lowercase letters we store how many times it appears in `a` (`freqA`) and in `b` (`freqB`).

2. **Build prefix sums** of these frequency arrays.  
   `prefA[i]` = number of characters in `a` that are **≤** character `('a'+i)`.  
   `prefB[i]` = number of characters in `b` that are **≤** character `('a'+i)`.

3. **Try all possible split letters** `c` (from `'a'` to `'y'`).  
   - **Condition 1** (a < b):  
     *Make all `a` ≤ `c` and all `b` > `c`*  
     `cost1 = (lenA - prefA[c]) + prefB[c]`
   - **Condition 2** (b < a):  
     *Make all `b` ≤ `c` and all `a` > `c`*  
     `cost2 = (lenB - prefB[c]) + prefA[c]`

4. **Condition 3** (single distinct letter each).  
   For each string we only need to change all characters that are **not** the most frequent one.  
   `cost3 = (lenA - maxFreqA) + (lenB - maxFreqB)`

5. The answer is the minimum over all `cost1`, `cost2`, and `cost3`.

The algorithm runs in **O(n + 26)** time and uses only **O(26)** extra memory.

---

### 4️⃣ Code Implementations

#### 🔶 Java

```java
public class Solution {
    public int minCharacters(String a, String b) {
        int lenA = a.length(), lenB = b.length();
        int[] freqA = new int[26], freqB = new int[26];
        int maxFreqA = 0, maxFreqB = 0;

        for (int i = 0; i < lenA; i++) {
            int idx = a.charAt(i) - 'a';
            freqA[idx]++;
            maxFreqA = Math.max(maxFreqA, freqA[idx]);
        }

        for (int i = 0; i < lenB; i++) {
            int idx = b.charAt(i) - 'a';
            freqB[idx]++;
            maxFreqB = Math.max(maxFreqB, freqB[idx]);
        }

        // Condition 3: both strings single distinct letter
        int ans = (lenA - maxFreqA) + (lenB - maxFreqB);

        // Build prefix sums
        for (int i = 1; i < 26; i++) {
            freqA[i] += freqA[i - 1];
            freqB[i] += freqB[i - 1];
        }

        // Try every split letter 'a' .. 'y'
        for (int i = 0; i < 25; i++) {
            int cost1 = (lenA - freqA[i]) + freqB[i];     // a <= i, b > i
            int cost2 = (lenB - freqB[i]) + freqA[i];     // b <= i, a > i
            ans = Math.min(ans, Math.min(cost1, cost2));
        }

        return ans;
    }
}
```

#### 🔶 Python

```python
class Solution:
    def minCharacters(self, a: str, b: str) -> int:
        len_a, len_b = len(a), len(b)

        freq_a = [0] * 26
        freq_b = [0] * 26

        max_a = max_b = 0
        for ch in a:
            idx = ord(ch) - 97
            freq_a[idx] += 1
            max_a = max(max_a, freq_a[idx])

        for ch in b:
            idx = ord(ch) - 97
            freq_b[idx] += 1
            max_b = max(max_b, freq_b[idx])

        # Condition 3
        ans = (len_a - max_a) + (len_b - max_b)

        # Prefix sums
        for i in range(1, 26):
            freq_a[i] += freq_a[i - 1]
            freq_b[i] += freq_b[i - 1]

        # Split letters a .. y
        for i in range(25):
            cost1 = (len_a - freq_a[i]) + freq_b[i]   # a <= i, b > i
            cost2 = (len_b - freq_b[i]) + freq_a[i]   # b <= i, a > i
            ans = min(ans, cost1, cost2)

        return ans
```

#### 🔶 C++

```cpp
class Solution {
public:
    int minCharacters(string a, string b) {
        int lenA = a.size(), lenB = b.size();
        vector<int> freqA(26), freqB(26);
        int maxA = 0, maxB = 0;

        for (char ch : a) {
            int idx = ch - 'a';
            freqA[idx]++;
            maxA = max(maxA, freqA[idx]);
        }

        for (char ch : b) {
            int idx = ch - 'a';
            freqB[idx]++;
            maxB = max(maxB, freqB[idx]);
        }

        // Condition 3
        int ans = (lenA - maxA) + (lenB - maxB);

        // Prefix sums
        for (int i = 1; i < 26; ++i) {
            freqA[i] += freqA[i - 1];
            freqB[i] += freqB[i - 1];
        }

        // Split letters 'a' .. 'y'
        for (int i = 0; i < 25; ++i) {
            int cost1 = (lenA - freqA[i]) + freqB[i];   // a <= i, b > i
            int cost2 = (lenB - freqB[i]) + freqA[i];   // b <= i, a > i
            ans = min(ans, min(cost1, cost2));
        }

        return ans;
    }
};
```

> All three solutions run in **O(|a| + |b|)** time and use only **26** integers of memory.

---

### 5️⃣ Complexity Breakdown

| Step | Time | Memory |
|------|------|--------|
| Counting frequencies | **O(n)** | **O(26)** |
| Building prefix sums | **O(26)** | – |
| Splitting over 25 letters | **O(25)** | – |
| **Total** | **O(n + 26)** → effectively **O(n)** | **O(26)** → effectively **O(1)** |

With **`n ≤ 10⁵`**, this solution easily fits the limits of LeetCode’s online judge.

---

### 6️⃣ Edge‑Case Checklist

| Edge Case | Why It Matters | How The Code Handles It |
|-----------|----------------|------------------------|
| Empty strings | No changes needed | `lenA` or `lenB` may be 0 – formulas still work. |
| Strings already satisfy a condition | Return `0` | Minimum will be computed as `0`. |
| All characters same | Condition 3 is optimal | `maxFreq` equals the string length → cost is `0`. |
| One string length = 1 | Works the same | Prefix sums handle it correctly. |
| Splitting at `'y'` only | We need `'z'`‑only for the other string – impossible | We purposely loop only to `'y'` (i < 25). |

---

### 7️⃣ Good / Bad / Ugly Analysis

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithmic elegance** | Uses simple frequency + prefix sums → very readable | None; the logic is straightforward | None – memory usage is negligible (26 ints) |
| **Performance** | Runs in linear time → passes 10⁵‑length cases | Same | None |
| **Maintainability** | Separate loop for condition 3 keeps code tidy | Same | None |
| **Potential pitfalls** | Forgetting to compute prefix sums or to iterate only up to `'y'` | None | None |
| **Learning takeaway** | Showcases how to transform a “split‑point” constraint into a prefix‑sum problem | None | None |

---

### 8️⃣ Quick Test Cases

| `a` | `b` | Expected |
|-----|-----|----------|
| `"aba"` | `"caa"` | `2` |
| `"zzzz"` | `"aaaa"` | `4` |
| `"abc"` | `"abc"` | `3` |
| `"a"` | `"z"` | `0` (condition 1 already true) |
| `"bbb"` | `"bbb"` | `0` (condition 3 already true) |

Run the provided functions to verify the output.

---

### 9️⃣ Summary

*LeetCode 1737* teaches you how to turn a seemingly complex “string‑splitting” task into a simple frequency‑prefix‑sum problem.  
The key take‑aways:

- **Count** → **prefix sum** → **iterate over 25 split letters**  
- Condition 3 is just “most frequent letter” per string  
- Overall **O(n)** time, **O(1)** space

Whether you’re polishing your Java, Python, or C++ interview stack, this problem is a must‑know for the *medium* tier of coding interviews and can earn you a big win on your next job interview.

Happy coding! 🚀