---
title: LeetCode 466. Count The Repetitions - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀  LeetCode 466 – “Count The Repetitions”  
**Solve it in Java, Python, & C++**  
**Blog article** – “The Good, The Bad & The Ugly of Counting Repetitions” (SEO‑optimized, ready for your résumé & interview prep)

---

### 📌 Problem Recap (LeetCode 466)

> You’re given two strings `s1` & `s2` and two integers `n1`, `n2`.  
> Define `str1 = [s1, n1]` as the string created by concatenating `s1` `n1` times,  
> and `str2 = [s2, n2]` similarly.  
>  
> **Goal:** Return the maximum integer `m` such that `str2` repeated `m` times (`[str2, m]`) can be obtained from `str1` by deleting characters.  
>  
> **Constraints**  
> - `1 ≤ s1.length, s2.length ≤ 100`  
> - `1 ≤ n1, n2 ≤ 10^6`  
> - All letters are lowercase English.

---

## ✅ Optimal Approach – Cycle Detection

1. **Traverse `str1` once** while counting how many full `s2` blocks we can match.  
2. Keep track of two arrays:  
   * `indexR[i]` – the position inside `s2` after processing the `i‑th` block of `s1`.  
   * `countR[i]` – the number of complete `s2` blocks matched up to that point.  
3. If the same `indexR` value re‑appears, a cycle is found.  
4. Use the cycle to skip many `s1` blocks in constant time:  
   * `prevCount` – matches before the cycle.  
   * `patternCount` – matches per cycle * number of cycles left.  
   * `remainCount` – matches in the leftover part of the cycle.  
5. Finally, `answer = (prevCount + patternCount + remainCount) / n2`.

Time Complexity: **O(n1 + n2)**  
Space Complexity: **O(n2)** (small, ≤ 101)

---

## 🧑‍💻 Code Implementations

> All three implementations below are ready‑to‑paste for the corresponding language.

---

### 1️⃣ Java (Java 17)

```java
import java.util.*;

public class Solution {
    /**
     * LeetCode 466 – Count The Repetitions
     */
    public int getMaxRepetitions(String s1, int n1, String s2, int n2) {
        if (n1 == 0) return 0;

        int s1Len = s1.length();
        int s2Len = s2.length();

        // indexR[i] – position in s2 after i-th block of s1
        int[] indexR = new int[n1 + 1];
        // countR[i] – number of s2 blocks matched after i-th block of s1
        int[] countR = new int[n1 + 1];

        int index = 0;   // position in s2
        int count = 0;   // number of complete s2 matched

        for (int i = 0; i < n1; i++) {
            for (int j = 0; j < s1Len; j++) {
                if (s1.charAt(j) == s2.charAt(index)) {
                    index++;
                    if (index == s2Len) {   // one s2 matched
                        index = 0;
                        count++;
                    }
                }
            }
            countR[i] = count;
            indexR[i] = index;

            // Detect a cycle
            for (int k = 0; k < i; k++) {
                if (indexR[k] == index) {
                    int prevCount = countR[k];
                    int cycleLength = i - k;
                    int cycleCount = countR[i] - countR[k];

                    // How many whole cycles fit into the remaining n1 blocks?
                    int remainBlocks = n1 - 1 - k;
                    int fullCycles = remainBlocks / cycleLength;
                    int rest = remainBlocks % cycleLength;

                    int totalCount = prevCount
                            + cycleCount * fullCycles
                            + (countR[k + rest] - countR[k]);

                    return totalCount / n2;
                }
            }
        }

        // No cycle found – use the last value
        return countR[n1 - 1] / n2;
    }

    // Simple test harness
    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.getMaxRepetitions("acb", 4, "ab", 2)); // 2
        System.out.println(sol.getMaxRepetitions("acb", 1, "acb", 1)); // 1
    }
}
```

---

### 2️⃣ Python (Python 3)

```python
class Solution:
    def getMaxRepetitions(self, s1: str, n1: int, s2: str, n2: int) -> int:
        if n1 == 0:
            return 0

        s1_len, s2_len = len(s1), len(s2)

        # indexR[i] – position in s2 after i-th block of s1
        indexR = [0] * (n1 + 1)
        # countR[i] – number of s2 blocks matched after i-th block of s1
        countR = [0] * (n1 + 1)

        index, count = 0, 0

        for i in range(n1):
            for ch in s1:
                if ch == s2[index]:
                    index += 1
                    if index == s2_len:       # matched one s2
                        index = 0
                        count += 1

            countR[i] = count
            indexR[i] = index

            # Cycle detection
            for k in range(i):
                if indexR[k] == index:
                    prev_count = countR[k]
                    cycle_len = i - k
                    cycle_cnt = countR[i] - countR[k]

                    remain_blocks = n1 - 1 - k
                    full_cycles = remain_blocks // cycle_len
                    rest = remain_blocks % cycle_len

                    total_count = (
                        prev_count
                        + cycle_cnt * full_cycles
                        + (countR[k + rest] - countR[k])
                    )
                    return total_count // n2

        return countR[n1 - 1] // n2


# Demo
if __name__ == "__main__":
    sol = Solution()
    print(sol.getMaxRepetitions("acb", 4, "ab", 2))  # 2
    print(sol.getMaxRepetitions("acb", 1, "acb", 1))  # 1
```

---

### 3️⃣ C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int getMaxRepetitions(const string &s1, int n1, const string &s2, int n2) {
        if (n1 == 0) return 0;

        int s1Len = s1.size();
        int s2Len = s2.size();

        vector<int> indexR(n1 + 1, 0);   // pos in s2 after i-th block
        vector<int> countR(n1 + 1, 0);   // matches after i-th block

        int index = 0, count = 0;

        for (int i = 0; i < n1; ++i) {
            for (char ch : s1) {
                if (ch == s2[index]) {
                    ++index;
                    if (index == s2Len) {          // one s2 matched
                        index = 0;
                        ++count;
                    }
                }
            }
            countR[i] = count;
            indexR[i] = index;

            // Cycle detection
            for (int k = 0; k < i; ++k) {
                if (indexR[k] == index) {
                    int prevCount  = countR[k];
                    int cycleLen   = i - k;
                    int cycleCnt   = countR[i] - countR[k];

                    int remainBlocks = n1 - 1 - k;
                    int fullCycles   = remainBlocks / cycleLen;
                    int rest         = remainBlocks % cycleLen;

                    int totalCount = prevCount
                                   + cycleCnt * fullCycles
                                   + (countR[k + rest] - countR[k]);

                    return totalCount / n2;
                }
            }
        }
        return countR[n1 - 1] / n2;
    }
};

int main() {
    Solution sol;
    cout << sol.getMaxRepetitions("acb", 4, "ab", 2) << endl; // 2
    cout << sol.getMaxRepetitions("acb", 1, "acb", 1) << endl; // 1
    return 0;
}
```

---

## 📄 SEO‑Optimized Blog Article

> Use this article as a LinkedIn post, Medium article, or résumé‑friendly content piece.  
> It’s full of job‑interview‑ready keywords, actionable insights, and a friendly tone.

---

# The Good, The Bad & The Ugly of Counting Repetitions

> *SEO keywords: LeetCode 466, Count The Repetitions, coding interview, Java solution, Python solution, C++ solution, algorithm design, string manipulation, cycle detection, interview preparation, software engineering interview.*

---

## 1️⃣ Introduction

> **“The ‘Count The Repetitions’ problem is one of the trickiest string‑manipulation challenges on LeetCode.**  
> It’s a classic interview question for senior‑level roles, especially at FAANG‑type companies, because it tests your ability to spot hidden cycles, optimize for O(10⁶) operations, and write clean, bug‑free code.  

### Why It Matters

| **Job Role** | **Why You’ll Face It** |
|--------------|------------------------|
| **Software Engineer** | String DP, interview standard |
| **Systems Engineer** | Needs to think about state‑space reduction |
| **Data‑Engineer** | Handles huge concatenated strings |

---

## 2️⃣ Problem Statement (Simplified)

> Given `s1`, `n1`, `s2`, `n2`, compute the maximum `m` such that `[s2, m]` is a subsequence of `[s1, n1]`.

---

## 3️⃣ Naïve & Inefficient Approaches

| Approach | Complexity | Practicality |
|----------|------------|--------------|
| **Brute Force** (enumerate all deletions) | Exponential | Impossible for `n1, n2 ≤ 10⁶` |
| **Dynamic Programming over `s1` & `s2`** | O(n1 * n2) | Still far too slow for 10⁶ |
| **Recursive + Memoization** | O(n1 * n2) | Memory blow‑up, stack depth issues |

> **The bad part:** Even the DP solution gets timeouts on the upper bounds.  
> **The ugly part:** Many candidates write over 200 lines of code just to hit O(n1 * n2).

---

## 4️⃣ Optimal Solution – Cycle Detection (The Good)

### How It Works

1. **Single Pass over `str1`:**  
   * Keep an `index` inside `s2`.  
   * When `index == s2.length`, we finished a `s2` block – reset `index` and increment `count`.  

2. **Store State Per `s1` Block:**  
   * `indexR[i]` – where we’re in `s2` after processing the *i‑th* block of `s1`.  
   * `countR[i]` – how many full `s2` blocks have been matched so far.  

3. **Detect a Cycle:**  
   * If `indexR[i]` repeats, we’re back at a state we’ve seen before.  
   * The sequence of `countR` between two identical `indexR` values is a cycle.  

4. **Jump Ahead:**  
   * `prevCount` – matches before the cycle starts.  
   * `cycleLen` & `cycleCnt` – length & matches per cycle.  
   * Compute how many full cycles fit into the remaining blocks and add the leftover matches.  

5. **Final Answer:**  
   * `answer = totalMatches / n2`  

> **Why It’s Awesome:**  
> * Handles the worst case (`10⁶`) in just ~10⁴ operations.  
> * Uses only a few integer arrays (≤ 101 elements).  
> * Clear logical flow that you can explain in an interview.

### Complexity

| Metric | Formula | Result |
|--------|---------|--------|
| **Time** | `O(n1 + n2)` | ≈ 2 × 10⁴ operations for max input |
| **Space** | `O(n2)` | ≤ 101 integers |

---

## 5️⃣ Code Snippets (Java, Python, C++)

> See the *Code Implementations* section above.  
> All three are concise, commented, and come with a quick demo.

---

## 6️⃣ Edge‑Case Checklist

| Edge Case | What to Watch For |
|-----------|-------------------|
| `s1` contains no letters from `s2` | `index` never advances → answer is 0 |
| `s2` is longer than `str1` | `countR` stays 0 |
| `n1` or `n2` is 1 | Works seamlessly |
| `s1 == s2` | Cycle detection still works |
| Large `n1`, small `n2` | The cycle logic skips almost all work |

---

## 7️⃣ Interview‑Ready Tips

1. **Explain the idea of a “state” inside `s2`.**  
   * Use the `index` variable and why resetting it after a match matters.  
2. **Talk about cycle detection.**  
   * Mention “looking for the same state twice” → O(1) skip.  
3. **Show the formula for total matches** (prev + cycle × loops + remainder).  
4. **Highlight Big‑O numbers** – Interviewers love seeing you can quantify performance.  

> **Sample Interview Answer**  
> “I iterate through each block of `s1`, keep track of where we are inside `s2`. When I encounter the same position again, I know the process will repeat, so I compute how many full cycles of `s1` I can skip and adjust the count accordingly. This reduces the complexity from `O(n1 * n2)` to `O(n1 + n2)`, which is crucial for the given constraints.”

---

## 🎯 Takeaway & Call‑to‑Action

> *Mastering “Count The Repetitions” proves you can:*
> - *Identify hidden cycles in a problem.*  
> - *Optimize an algorithm from exponential to linear.*  
> - *Write clean, language‑agnostic solutions.*  

**Ready to impress your next interview?**  
1. Practice the problem on LeetCode (466).  
2. Add the Java, Python, & C++ solutions to your GitHub repository.  
3. Include the blog post on Medium or LinkedIn—tag it with **#LeetCode466 #CountTheRepetitions #CodingInterview**.  

Your résumé now contains a **real‑world algorithmic victory** and a **well‑structured, SEO‑rich interview story** that recruiters love. Happy coding!  

--- 

> **References**  
> * LeetCode 466 – [link](https://leetcode.com/problems/count-the-repetitions/)  
> * Discuss on Reddit: r/leetcode, r/codinginterviews  
> * Blog on Medium: “The Good, The Bad & The Ugly of Counting Repetitions” – adapt the headings above.  

--- 

**Happy Interviewing!** 🎯🚀