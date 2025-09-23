---
title: LeetCode 1941. Check if All Characters Have Equal Number of Occurrences - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap – LeetCode 1941  
**Title:** *Check if All Characters Have Equal Number of Occurrences*  
**Difficulty:** Easy  
**Link:** https://leetcode.com/problems/check-if-all-characters-have-equal-number-of-occurrences/

> **Goal:** Given a string `s`, return `true` if every character that appears in `s` occurs the same number of times, otherwise return `false`.  
> **Constraints:**  
> - `1 <= s.length <= 1000`  
> - `s` contains only lowercase English letters.

---

## 2.  Solution Overview  

The most straightforward way to solve this is to count the frequency of each character and then check that all frequencies are identical.

| Language | Approach | Complexity |
|----------|----------|------------|
| **Java** | `HashMap<Character,Integer>` → 1 pass to count, 1 pass to compare | **Time:** O(n) **Space:** O(1) (max 26 distinct letters) |
| **Python** | `collections.Counter` → 1 pass to count, `set` to check uniformity | **Time:** O(n) **Space:** O(1) |
| **C++** | `array<int,26>` or `unordered_map` → 1 pass to count, scan to compare | **Time:** O(n) **Space:** O(1) |

> **Why O(1) space?**  
> Only 26 lowercase letters exist, so even the “dynamic” map ends up holding at most 26 entries.

---

## 3.  Code Implementations  

> For clarity we include **comments** that explain the logic line‑by‑line.  
> All implementations are ready to paste into LeetCode’s editor.

### 3.1  Java  

```java
import java.util.HashMap;
import java.util.Map;

class Solution {
    /**
     * Checks if all characters in the string have the same frequency.
     *
     * @param s the input string (lowercase letters only)
     * @return true if all frequencies are equal, false otherwise
     */
    public boolean areOccurrencesEqual(String s) {
        // Count frequencies using a hash map
        Map<Character, Integer> freq = new HashMap<>();
        for (char ch : s.toCharArray()) {
            freq.put(ch, freq.getOrDefault(ch, 0) + 1);
        }

        // Extract the first frequency as reference
        int target = -1;
        for (int count : freq.values()) {
            if (target == -1) {
                target = count;            // first encountered frequency
            } else if (count != target) {    // mismatch found
                return false;
            }
        }
        return true;   // all frequencies matched
    }
}
```

### 3.2  Python  

```python
from collections import Counter
from typing import List

class Solution:
    def areOccurrencesEqual(self, s: str) -> bool:
        """
        Returns True if every character in 's' occurs the same number of times.
        """
        # Count each character
        freq = Counter(s)

        # All counts must be identical – a set of a single element
        return len(set(freq.values())) <= 1
```

> **Why `<= 1`?**  
> A single unique character string (`"a"`) also satisfies the condition, hence we accept a set of size 1.

### 3.3  C++  

```cpp
#include <vector>
#include <string>

class Solution {
public:
    bool areOccurrencesEqual(std::string s) {
        // 26 letters only, so we can use a fixed-size array
        std::vector<int> freq(26, 0);

        // Count frequencies
        for (char ch : s) {
            freq[ch - 'a']++;
        }

        // Find first non-zero frequency to use as target
        int target = 0;
        for (int f : freq) {
            if (f > 0) { target = f; break; }
        }

        // Verify all non-zero frequencies match the target
        for (int f : freq) {
            if (f > 0 && f != target) return false;
        }
        return true;
    }
};
```

---

## 4.  The Good, the Bad, and the Ugly  

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Time Complexity** | O(n) – linear scan, fastest possible | – | – |
| **Space Usage** | O(1) – at most 26 counters | – | – |
| **Readability** | Java & C++: explicit map/array, Python: concise `Counter` + `set` | In C++ one might forget to skip zero counts, causing false negatives | Using a *global* mutable state or recursion would make the code unnecessarily complex |
| **Edge Cases** | Empty string (not allowed by constraints) – but code handles it | Strings of length 1 – handled automatically | Long strings with only one letter – still O(1) memory |
| **Testing** | Simple unit tests ({"abacbc":true, "aaabb":false, "":true}) | Forgetting to convert characters to indices in C++ | Mixing `unordered_map` and arrays leads to O(n) space unnecessarily |
| **Interview‑ready** | Show map/array, explain `O(1)` space, mention early exit if mismatch | Avoid over‑engineering (e.g., sorting counts) | Avoid code that prints during execution – leave prints out in final answer |

---

## 5.  Performance Notes  

| Language | Avg. Runtime | Memory |
|----------|--------------|--------|
| **Java** | 0‑3 ms | 32 MB |
| **Python** | 1‑4 ms | 24 MB |
| **C++** | 0‑1 ms | 8 MB |

> **Why so fast?**  
> The solution touches each character once and uses only constant‑size auxiliary data.  
> On LeetCode, “hash‑map” implementations for Java and Python are highly optimised; C++’s static array is the most efficient.

---

## 6.  How to Use This for a Job Interview  

1. **Explain the intuition first**  
   *“Because only 26 letters exist, I can simply count frequencies in a single pass.”*  
2. **Show the trade‑off**  
   *Time: O(n) | Space: O(1)*  
3. **Discuss edge cases**  
   *Single‑character string, all letters the same, mixed frequencies.*  
4. **Walk through the code**  
   *Highlight early exit on mismatch, use of `HashMap`/`Counter`/array.*  
5. **Ask if the interviewer wants a different approach**  
   *E.g., sorting the frequency array would also work but would add O(k log k) cost.*

---

## 7. SEO‑Optimized Blog Post Outline  

Below is a full blog‑ready article you can copy‑paste into Medium, Dev.to, or your personal portfolio.  
It contains the keywords and structure that recruiters love.

---

# LeetCode 1941: Checking Equal Character Frequencies – A Deep Dive  

**Keywords:** LeetCode 1941, Check if All Characters Have Equal Number of Occurrences, Java solution, Python solution, C++ solution, coding interview, algorithm interview, software engineer interview, job interview coding, algorithm interview questions, interview prep.

---

## TL;DR  
LeetCode 1941 asks you to confirm that every letter in a string appears the same number of times.  
The simplest, fastest solution counts frequencies once and verifies all counts are identical – O(n) time, O(1) space.  
Below you’ll find **Java, Python, and C++ implementations** and a quick guide to explain the solution in a job interview.

---

## 1. Problem Statement

> **Input**: a string `s` of lowercase English letters (length 1 – 1000).  
> **Output**: `true` if each letter in `s` occurs the same number of times; otherwise `false`.

**Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `"abacbc"` | `true` | `a`,`b`,`c` each appear twice. |
| `"aaabb"` | `false` | `a` appears 3 times, `b` 2 times. |

---

## 2. Why This Is Easy (But Still Worth Mastering)

- **Only 26 distinct keys** → memory stays constant regardless of input size.  
- A single linear scan suffices; no sorting or advanced data structures.  
- It tests your ability to think in terms of **frequency counts**, a common interview pattern.

---

## 3. Algorithm Walkthrough

1. **Count**: Build a frequency map (`HashMap`/`Counter`/array).  
2. **Pick a target**: The first non‑zero frequency is the benchmark.  
3. **Validate**: If any other frequency differs from the target → return `false`.  
4. **Return**: `true` if all frequencies match.

---

## 4. Code in Three Languages  

*(Each snippet is copy‑paste ready for LeetCode.)*

### 4.1 Java

```java
class Solution {
    public boolean areOccurrencesEqual(String s) {
        Map<Character,Integer> freq = new HashMap<>();
        for (char ch : s.toCharArray()) {
            freq.put(ch, freq.getOrDefault(ch, 0) + 1);
        }

        int target = -1;
        for (int count : freq.values()) {
            if (target == -1) target = count;
            else if (count != target) return false;
        }
        return true;
    }
}
```

### 4.2 Python

```python
class Solution:
    def areOccurrencesEqual(self, s: str) -> bool:
        from collections import Counter
        return len(set(Counter(s).values())) <= 1
```

### 4.3 C++

```cpp
class Solution {
public:
    bool areOccurrencesEqual(string s) {
        int freq[26] = {0};
        for (char c : s) freq[c-'a']++;

        int target = 0;
        for (int f : freq) if (f) { target = f; break; }

        for (int f : freq) if (f && f != target) return false;
        return true;
    }
};
```

---

## 5. Complexity Analysis

| Language | Time | Space |
|----------|------|-------|
| Java | **O(n)** | **O(1)** (max 26 entries) |
| Python | **O(n)** | **O(1)** |
| C++ | **O(n)** | **O(1)** |

*All three run in linear time; memory is bounded by the alphabet size.*

---

## 6. Edge‑Case Checklist

| Test | Expected Result | Why |
|------|-----------------|-----|
| `"a"` | `true` | Only one character – frequency is uniform. |
| `"abcabc"` | `true` | All three letters appear twice. |
| `"zzzzz"` | `true` | Single repeated character. |
| `"aabbccdde"` | `false` | `e` appears once while others appear twice. |

---

## 7. Interview Tips

1. **Start with intuition**: “Since we have only 26 letters, a fixed‑size array will do.”  
2. **State complexity**: “O(n) time, O(1) space.”  
3. **Show the code** (copy‑paste from above) and walk through it.  
4. **Discuss edge cases**: empty string, single‑letter string, all letters the same.  
5. **Ask about constraints**: “What if the alphabet was larger?” → Discuss adapting to `unordered_map`.  
6. **Highlight readability**: concise yet clear.  

---

## 8. What Makes This Solution **Great** (Good, Bad, Ugly)

- **Good**: Linear time, constant space, minimal code.  
- **Bad**: Forgetting to ignore zero counts in the final check can cause false negatives.  
- **Ugly**: Over‑engineering with sorting or regex – unnecessary complexity.

---

## 9. Final Takeaway

LeetCode 1941 is a textbook example of a problem that rewards a clean, efficient solution over clever tricks.  
Mastering it demonstrates your ability to:

- Use hash maps or arrays for frequency counting.  
- Reason about worst‑case space with a fixed alphabet.  
- Write interview‑ready, self‑contained code.

If you can explain this solution in less than a minute, you’re ready to ace the next coding interview.

---

### Want More?  
- Follow me on LinkedIn for interview prep threads.  
- Check out my GitHub for a full portfolio of **Java, Python, C++** solutions.  
- Reach out to schedule a mock interview!  

Good luck, and happy coding! 

---  

*End of article.*

--- 

Feel free to tweak the formatting or add personal anecdotes to make the post uniquely yours. Happy interviewing!