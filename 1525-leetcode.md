---
title: LeetCode 1525. Number of Good Ways to Split a String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1525. Number of Good Ways to Split a String  
*(LeetCode – Medium)*  

> **Problem**  
> You are given a string `s`.  
> A split is called **good** if you can split `s` into two non‑empty strings `sLeft` and `sRight` such that  
> - `sLeft + sRight == s`  
> - the number of **distinct letters** in `sLeft` **equals** the number of distinct letters in `sRight`.  
> Return the number of good splits you can make in `s`.

> **Constraints**  
> * `1 ≤ s.length ≤ 10⁵`  
> * `s` consists only of lowercase English letters.

---

## 1. Intuition  

The key observation is that we only care about *how many* distinct letters are in the left and right parts, **not** *which* letters.  
If we know the frequency of every letter in the whole string, we can slide a “split point” from left to right, updating:

* `leftDistinct` – how many distinct letters have appeared on the left side.
* `rightDistinct` – how many distinct letters are still on the right side.

Because the alphabet size is fixed (26), we can keep a 26‑element array for frequencies – O(1) memory.

---

## 2. Algorithm (two‑pointer / sliding split)

```
1. Count the frequency of each character in the entire string → freq[26].
2. rightDistinct  = number of indices i where freq[i] > 0
   leftDistinct   = 0
   leftCount[26]  = 0          // frequencies that have moved to the left
   result         = 0

3. For i from 0 to n-2      // split after i, right part is non‑empty
       ch = s[i] – 'a'

       // move ch from right side to left side
       freq[ch]--                // remove from right
       if freq[ch] == 0:         // no longer present on the right
           rightDistinct--

       leftCount[ch]++
       if leftCount[ch] == 1:    // first time we see it on the left
           leftDistinct++

       if leftDistinct == rightDistinct:
           result++

4. Return result
```

**Why it works**

*At every iteration* the left part consists of the first `i+1` characters, the right part of the rest.  
`leftDistinct` and `rightDistinct` are updated in constant time.  
Thus every split is examined exactly once, and we count all good splits.

---

## 3. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Counting frequencies | **O(n)** | **O(1)** (26 ints) |
| Sliding loop | **O(n)** | **O(1)** |
| **Total** | **O(n)** | **O(1)** |

The algorithm easily meets the `10⁵`‑length limit.

---

## 4. Reference Implementations  

Below are clean, beginner‑friendly implementations in **Java**, **Python**, and **C++**.

---

### 4.1 Java

```java
import java.util.*;

public class Solution {
    public int numSplits(String s) {
        int n = s.length();
        if (n <= 1) return 0;

        // frequency of each letter in the whole string
        int[] freq = new int[26];
        for (int i = 0; i < n; i++) {
            freq[s.charAt(i) - 'a']++;
        }

        int rightDistinct = 0;
        for (int f : freq) if (f > 0) rightDistinct++;

        int leftDistinct = 0;
        int[] leftCount = new int[26];
        int result = 0;

        for (int i = 0; i < n - 1; i++) {
            int idx = s.charAt(i) - 'a';

            // move char from right to left
            freq[idx]--;
            if (freq[idx] == 0) rightDistinct--;

            leftCount[idx]++;
            if (leftCount[idx] == 1) leftDistinct++;

            if (leftDistinct == rightDistinct) result++;
        }
        return result;
    }
}
```

---

### 4.2 Python

```python
class Solution:
    def numSplits(self, s: str) -> int:
        n = len(s)
        if n <= 1:
            return 0

        # freq of each char in the whole string
        freq = [0] * 26
        for ch in s:
            freq[ord(ch) - 97] += 1

        right_distinct = sum(1 for f in freq if f > 0)
        left_distinct = 0
        left_count = [0] * 26
        result = 0

        for i in range(n - 1):
            idx = ord(s[i]) - 97
            freq[idx] -= 1
            if freq[idx] == 0:
                right_distinct -= 1

            left_count[idx] += 1
            if left_count[idx] == 1:
                left_distinct += 1

            if left_distinct == right_distinct:
                result += 1

        return result
```

---

### 4.3 C++

```cpp
class Solution {
public:
    int numSplits(string s) {
        int n = s.size();
        if (n <= 1) return 0;

        vector<int> freq(26, 0);
        for (char c : s) freq[c - 'a']++;

        int rightDistinct = 0;
        for (int f : freq) if (f > 0) rightDistinct++;

        int leftDistinct = 0;
        vector<int> leftCnt(26, 0);
        int result = 0;

        for (int i = 0; i < n - 1; ++i) {
            int idx = s[i] - 'a';
            freq[idx]--;
            if (freq[idx] == 0) rightDistinct--;

            leftCnt[idx]++;
            if (leftCnt[idx] == 1) leftDistinct++;

            if (leftDistinct == rightDistinct) ++result;
        }
        return result;
    }
};
```

---

## 5. The Good, the Bad, and the Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Intuition** | Clear: distinct counts on each side | None – a naive solution could try all splits (`O(n²)`) | Over‑engineering: building suffix arrays or using sets for each split |
| **Time Complexity** | Linear `O(n)` | Quadratic `O(n²)` (naïve) | Still linear but with higher constants (e.g., map operations) |
| **Space Complexity** | Constant `O(1)` | Linear `O(n)` (if storing suffix sets) | Variable, could blow up on large alphabets |
| **Readability** | Very readable – 30‑line code | Messy – many nested loops | Obscure – heavy use of STL containers |

> **Bottom line**: The sliding split with two frequency arrays is the sweet spot – fast, memory‑light, and easy to understand.

---

## 6. SEO‑Optimized Blog Article

> **Title:**  
> *Cracking LeetCode 1525 – Number of Good Ways to Split a String (Java, Python, C++)*  

> **Meta‑Description:**  
> Learn how to solve LeetCode 1525 in Java, Python, and C++ with a linear‑time algorithm. Discover the trick that makes this “good split” problem a breeze for your next coding interview.

---

### 6.1 Introduction  

If you’re hunting for a job in software engineering, you’ll run into LeetCode problems that test both your algorithmic mindset and your coding style. One of the more subtle yet elegant questions is **1525. Number of Good Ways to Split a String**.  

In this article, we’ll walk through the problem statement, unpack the intuition, craft a clean solution, and provide ready‑to‑copy code in **Java**, **Python**, and **C++**. We’ll also discuss common pitfalls, why the sliding‑split approach is preferable, and how to present this solution in an interview.

---

### 6.2 Problem Recap  

> *You’re given a string `s`. A split is **good** if you can divide `s` into two non‑empty parts such that the number of distinct letters in each part is equal. Return the count of all good splits.*  

The string can be up to `10⁵` characters long and contains only lowercase letters.  

---

### 6.3 Why This Problem Is Interview‑Friendly  

1. **Array Manipulation** – Shows your comfort with fixed‑size arrays.  
2. **Two‑Pointer / Sliding Window** – A classic interview pattern.  
3. **Space Optimization** – The solution uses `O(1)` memory.  
4. **Edge‑Case Awareness** – Handling strings of length 1, all identical letters, etc.  

---

### 6.4 The “Good” Part: The Sliding‑Split Algorithm  

**Step 1: Count the whole string**  
*Why?*  
We need to know how many distinct letters exist on the right side initially.  

**Step 2: Move a split point**  
*Why?*  
When the split moves rightward, a character moves from the right side to the left side.  
We update:

* `leftDistinct` – increment when a character appears on the left for the first time.  
* `rightDistinct` – decrement when a character’s count on the right falls to zero.  

Because there are only 26 letters, the update is `O(1)`.  

**Step 3: Compare and count**  
If `leftDistinct == rightDistinct`, we’ve found a good split.

---

### 6.5 The “Bad” Part: Naïve Approaches

| Approach | Complexity | Why It Fails in Interviews |
|----------|------------|----------------------------|
| Try every split, build `Set` for each part | `O(n²)` | Too slow for `10⁵` |
| Use a `HashMap` for suffix counts | `O(n)` time, `O(n)` space | Unnecessary memory; still fine but less elegant |
| Precompute suffix arrays of distinct counts | `O(n)` time, `O(n)` space | Works, but hides the core idea |

---

### 6.6 The “Ugly” Part: Over‑Engineering

> *When people get excited about complexity, they sometimes throw in fancy data structures that only add noise.*  
Examples:

* Building a new `unordered_set` in each iteration in C++ (high constant overhead).  
* Using recursion with memoization but ignoring that the alphabet is fixed.

These can impress at first glance but risk losing the interviewer's attention.

---

### 6.7 Interview Tips

* **Explain the invariant** – “Every time the split moves, we’re just re‑balancing the two counters.”  
* **Show a dry run** – Pick `s = "ababa"` and walk through the loop.  
* **Mention edge cases** – Single‑character strings return `0`; strings like `"aaaa"` return `n-1`.  
* **Ask clarifying questions** – “Do we count the split after the last character?” – Helps avoid off‑by‑one errors.

---

### 6.8 Code Snippets

> **Java** – 30‑line solution that compiles in Java 17  
> **Python** – O(1) memory with simple lists  
> **C++** – STL vectors but still constant space  

*(See Section 4 for full code.)*

---

### 6.9 Wrap‑Up  

LeetCode 1525 is a prime example of how a solid understanding of array counters and a two‑pointer mindset yields a solution that is **fast, memory‑efficient, and easy to explain**.  

If you can articulate this algorithm and deliver clean Java/Python/C++ code, you’ll not only solve the problem in a fraction of a second but also impress interviewers with your clarity of thought.

Good luck on your coding journey!

---

### 6.10 Closing  

Remember:  
* Keep solutions **linear** whenever possible.  
* Prefer **constant‑space** tricks for problems with a fixed alphabet.  
* Always walk through edge cases before coding.  

Happy coding, and best of luck landing that dream role!  

--- 

*Feel free to bookmark this article and revisit it before your next interview. Happy interviewing!*  

--- 

**Author:**  
[Your Name] – Passionate about algorithms and mentoring aspiring software engineers.  

**Tags:** #LeetCode1525 #CodingInterview #Java #Python #C++ #Algorithm #TwoPointer #DataStructures  

--- 

*If you liked this article, subscribe to my newsletter for weekly LeetCode walkthroughs and interview prep tips.* 

--- 

That concludes the full, interview‑ready guide to solving LeetCode 1525. Feel free to adapt the style and structure for your own portfolio or blog!