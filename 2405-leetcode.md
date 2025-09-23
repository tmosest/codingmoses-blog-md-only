---
title: LeetCode 2405. Optimal Partition of String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📚 Mastering LeetCode 2405 – Optimal Partition of String  
*Java | Python | C++ | SEO‑Optimized Guide to Nail the Interview*

---

### Table of Contents  
1. [Problem Recap](#problem-recap)  
2. [Why This Problem Rocks for Interviews](#why-this-problem-rocks)  
3. [The “Good” – A One‑Pass Greedy Solution (O(n) Time, O(1) Space)](#the-good)  
4. [The “Bad” – A Straightforward HashSet Approach (O(n) Time, O(26) Space)](#the-bad)  
5. [The “Ugly” – Brute Force & DP (O(2ⁿ) Time, Not Practical)](#the-ugly)  
6. [Full Code Implementations](#full-code-implementations)  
   - Java  
   - Python  
   - C++  
7. [Time & Space Complexity Breakdown](#complexity)  
8. [Common Pitfalls & Edge Cases](#pitfalls)  
9. [Optimizing for the Interviewer](#optimizing)  
10. [Conclusion & Next Steps](#conclusion)

---

<a name="problem-recap"></a>
## 1. Problem Recap

**LeetCode 2405 – Optimal Partition of String**  
> Given a string `s` (only lowercase English letters), partition it into the fewest possible substrings such that each substring contains **unique** characters.  
> Return the minimal number of substrings.

Examples  
| Input | Output | Explanation |
|-------|--------|-------------|
| `"abacaba"` | `4` | `"a","ba","cab","a"` or `"ab","a","ca","ba"` |
| `"ssssss"` | `6` | Every `s` must stand alone |

**Constraints**  
- `1 <= s.length <= 10⁵`  
- Only lowercase letters (`'a'`–`'z'`)

---

<a name="why-this-problem-rocks"></a>
## 2. Why This Problem Rocks for Interviews

| Feature | Why It Matters |
|---------|----------------|
| **Greedy intuition** | Demonstrates ability to spot optimal substructure. |
| **Bit‑mask vs HashSet** | Shows knowledge of low‑level tricks and space optimisation. |
| **Linear scan** | Highlights time‑complexity reasoning (O(n)). |
| **Edge‑case handling** | Testcases include all same char, alternating pattern, long string. |

Interviewers love this problem because it is **easy to understand** yet **deep enough** to reveal cleverness.

---

<a name="the-good"></a>
## 3. The “Good” – One‑Pass Greedy with Bitmask

### Intuition
- Scan the string from left to right.  
- Keep a 26‑bit integer (`mask`) representing which characters have already appeared in the current substring.  
- When we encounter a character that is already set in `mask`, we *must* start a new substring: reset `mask`, increment the counter, and set the bit for the current character.

### Why It Works
- Any repeated character forces a partition because the current substring would violate uniqueness.  
- Starting a new substring at the earliest possible point (right when we see the duplicate) guarantees the *minimal* number of substrings – a classic greedy proof.

### Code (Java)
```java
public class Solution {
    public int partitionString(String s) {
        int mask = 0;      // 26‑bit mask for seen chars
        int count = 1;     // at least one substring
        for (int i = 0; i < s.length(); i++) {
            int bit = 1 << (s.charAt(i) - 'a');
            if ((mask & bit) != 0) {   // duplicate detected
                mask = 0;              // start new substring
                count++;
            }
            mask |= bit;                // mark char as seen
        }
        return count;
    }
}
```

### Why It’s Great
- **O(n) time** – one pass.  
- **O(1) space** – the mask is constant.  
- No heavy data structures; perfect for large input.

---

<a name="the-bad"></a>
## 4. The “Bad” – HashSet Approach

### Intuition
- Use a `HashSet<Character>` to keep track of seen characters in the current substring.  
- When encountering a duplicate, reset the set and start a new substring.

### Implementation
```java
public int partitionString(String s) {
    Set<Character> seen = new HashSet<>();
    int count = 1;
    for (char c : s.toCharArray()) {
        if (!seen.add(c)) {      // add returns false if already present
            seen.clear();        // new substring
            seen.add(c);
            count++;
        }
    }
    return count;
}
```

### Drawbacks
- **O(26) space** – still constant but heavier than bitmask.  
- Slightly slower due to hash operations.  
- Harder to explain to a non‑technical interviewer.

---

<a name="the-ugly"></a>
## 5. The “Ugly” – Brute Force / DP

### Brute Force
- Generate all possible partitions (`2^(n-1)` possibilities).  
- Check each for uniqueness → **exponential**.

### DP Approach
- `dp[i] = minimal substrings for s[0..i]`.  
- For each `i`, scan backward to find the longest valid substring ending at `i`.  
- Complexity: **O(n²)** – unacceptable for `n = 10⁵`.

**Why It’s Ugly**  
- Requires nested loops, higher time, and more logic to explain.  
- Interviewers expect O(n) for this problem.

---

<a name="full-code-implementations"></a>
## 6. Full Code Implementations

Below are clean, self‑contained solutions for Java, Python, and C++.

### Java (Bitmask Greedy)

```java
class Solution {
    public int partitionString(String s) {
        int mask = 0;      // 26‑bit bitmask
        int partitions = 1; // at least one substring
        for (int i = 0; i < s.length(); i++) {
            int bit = 1 << (s.charAt(i) - 'a');
            if ((mask & bit) != 0) {  // duplicate in current substring
                mask = 0;             // start new substring
                partitions++;
            }
            mask |= bit;               // mark char
        }
        return partitions;
    }
}
```

### Python (Bitmask Greedy)

```python
class Solution:
    def partitionString(self, s: str) -> int:
        mask = 0
        partitions = 1
        for ch in s:
            bit = 1 << (ord(ch) - 97)  # 97 == ord('a')
            if mask & bit:
                mask = 0
                partitions += 1
            mask |= bit
        return partitions
```

### C++ (Bitmask Greedy)

```cpp
class Solution {
public:
    int partitionString(string s) {
        int mask = 0;
        int partitions = 1;
        for (char c : s) {
            int bit = 1 << (c - 'a');
            if (mask & bit) {          // duplicate
                mask = 0;
                partitions++;
            }
            mask |= bit;
        }
        return partitions;
    }
};
```

All three solutions are **O(n)** time, **O(1)** space, and pass LeetCode’s hidden tests comfortably.

---

<a name="complexity"></a>
## 7. Time & Space Complexity Breakdown

| Approach | Time | Space |
|----------|------|-------|
| Bitmask Greedy | **O(n)** | **O(1)** |
| HashSet | **O(n)** | **O(26)** (constant) |
| DP (O(n²)) | **O(n²)** | **O(n)** |
| Brute Force | **O(2ⁿ)** | **O(1)** |

**Takeaway:** Use the bitmask for maximum speed and minimal memory.

---

<a name="pitfalls"></a>
## 8. Common Pitfalls & Edge Cases

| Pitfall | Fix |
|---------|-----|
| Forget to reset the counter before first iteration | Initialize `partitions = 1` (there’s always at least one substring). |
| Using a `HashSet` with `add()` without checking return value | Use `if (!set.add(c))` to detect duplicates. |
| Off‑by‑one in loops | Iterate strictly over `i < s.length()` or `for (char c : s.toCharArray())`. |
| Using a bitmask larger than 32 bits in C++ on 64‑bit compiler | `int mask` is fine (26 bits), but if you want to be safe use `int mask = 0;`. |
| Not handling empty string (though constraints say `len >= 1`) | Return `0` or `1` accordingly; usually not needed. |

---

<a name="optimizing"></a>
## 9. Optimizing for the Interviewer

1. **Explain the greedy proof quickly** – “We cut as soon as we see a duplicate because delaying would only increase the number of substrings.”  
2. **Mention the bitmask advantage** – “We only need 26 bits; no extra data structures.”  
3. **Show the code** – keep it clean, use meaningful variable names (`mask`, `partitions`).  
4. **Discuss complexity** – Emphasise O(n) and O(1) space.  
5. **Answer follow‑ups** – If asked about alternative solutions, mention the HashSet as a simpler but slightly heavier approach.  

---

<a name="conclusion"></a>
## 10. Conclusion & Next Steps

- **Optimal Partition of String** is a classic greedy problem that tests your ability to recognize when “cutting early” is the optimal strategy.  
- The **bitmask greedy solution** is the fastest, simplest, and most interview‑friendly.  
- Mastering this problem gives you confidence for similar questions: *Longest substring without repeating characters*, *String partition problems*, etc.

### Next Steps

1. **Practice** – Solve variants like “Longest substring without repeating characters” (LeetCode 3).  
2. **Read the editorial** – Understand the bitmask trick in depth.  
3. **Write a blog post** – Share this guide (like this one) to showcase your problem‑solving skills.  
4. **Apply** – Use this solution in your résumé under “Coding Interview Preparation”.

Good luck, and may your partitions always be optimal! 🚀

---  

**SEO Keywords Used:**  
- Optimal Partition of String LeetCode  
- LeetCode 2405 solution  
- Java Python C++ interview problem  
- greedy bitmask string partition  
- coding interview tips  
- algorithm interview prep  
- O(1) space string partition  
- interview questions string partition  
- job interview coding problem

---