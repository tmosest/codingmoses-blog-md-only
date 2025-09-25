---
title: LeetCode 522. Longest Uncommon Subsequence II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Leetcode 522 – **Longest Uncommon Subsequence II**  
### The Good, The Bad, and The Ugly  
> *A deep dive into three battle‑tested solutions (Java, Python, C++) that will help you ace the interview, plus an SEO‑friendly blog post that recruiters will love.*

---

### 📖 Problem Recap

> **Input**: `String[] strs` (2 ≤ |strs| ≤ 50, 1 ≤ |strs[i]| ≤ 10)  
> **Output**: length of the longest string that is a subsequence of **exactly one** array element, or **-1** if no such string exists.

> A *subsequence* of a string `s` can be obtained by deleting zero or more characters without changing the order of the remaining characters.

---

### 🎯 Why This Problem Rocks

- **Interview Goldmine**: Most companies ask it during system design or algorithm rounds.
- **Conceptual Breadth**: Touches on string manipulation, subsequence logic, and set theory.
- **Optimisation**: Easy to go from O(n²) to O(n) with a simple observation—ideal for demonstrating coding efficiency.

---

## ✅ Three Winning Solutions

Below are clean, production‑ready implementations in **Java**, **Python**, and **C++**. Each follows the same optimal strategy:  

1. **Count the frequency of each string**.  
2. If a string is unique (frequency = 1), its length is a candidate for the answer.  
3. The answer is the length of the longest unique string; otherwise `-1`.

Because a string that appears more than once can never be an uncommon subsequence (it’s a subsequence of itself and at least one other identical string), we only need to examine unique strings.  

### 1. Java (Java 17)

```java
import java.util.*;

public class Solution {
    public int findLUSlength(String[] strs) {
        // Count occurrences
        Map<String, Integer> freq = new HashMap<>();
        for (String s : strs) {
            freq.put(s, freq.getOrDefault(s, 0) + 1);
        }

        int best = -1;
        for (String s : strs) {
            if (freq.get(s) == 1) {          // unique string
                best = Math.max(best, s.length());
            }
        }
        return best;
    }

    // For quick local testing
    public static void main(String[] args) {
        var sol = new Solution();
        System.out.println(sol.findLUSlength(new String[]{"aba","cdc","eae"})); // 3
        System.out.println(sol.findLUSlength(new String[]{"aaa","aaa","aa"})); // -1
    }
}
```

**Key points**  
- Uses `HashMap` for O(1) lookups.  
- Linear scan (`O(n)`) over input; no nested loops, so it runs in **O(n)** time and **O(n)** space.

---

### 2. Python 3

```python
from collections import Counter
from typing import List

class Solution:
    def findLUSlength(self, strs: List[str]) -> int:
        freq = Counter(strs)          # frequency of each string
        # find the longest string that appears exactly once
        return max((len(s) for s in strs if freq[s] == 1), default=-1)

# Quick local test
if __name__ == "__main__":
    sol = Solution()
    print(sol.findLUSlength(["aba", "cdc", "eae"]))   # 3
    print(sol.findLUSlength(["aaa", "aaa", "aa"]))    # -1
```

**Highlights**  
- `Counter` gives O(n) counting.  
- The generator expression keeps memory usage low.  
- `default=-1` in `max` handles the “no unique string” case cleanly.

---

### 3. C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int findLUSlength(vector<string>& strs) {
        unordered_map<string, int> freq;
        for (const string& s : strs) ++freq[s];     // Count

        int best = -1;
        for (const string& s : strs)
            if (freq[s] == 1)                       // Unique
                best = max(best, (int)s.size());

        return best;
    }
};

// Simple main for testing
int main() {
    Solution sol;
    cout << sol.findLUSlength({"aba","cdc","eae"}) << endl; // 3
    cout << sol.findLUSlength({"aaa","aaa","aa"}) << endl;  // -1
    return 0;
}
```

**Why it’s slick**  
- `unordered_map` offers constant‑time inserts and lookups.  
- Uses range‑based for loops for readability.  
- Compiles in **O(n)** time and uses **O(n)** auxiliary space.

---

## 📈 Complexity Summary

| Language | Time | Space |
|----------|------|-------|
| Java     | O(n) | O(n)  |
| Python   | O(n) | O(n)  |
| C++      | O(n) | O(n)  |

*`n` = number of strings (≤ 50).*

---

## 📚 Deep Dive: Why the Simple Counter Works

1. **If a string appears more than once**  
   - It is a subsequence of itself and at least one other identical string → cannot be uncommon.

2. **If a string appears exactly once**  
   - No other string can be identical.  
   - Because all other strings are *shorter or equal* in length (max 10), the unique string cannot be a subsequence of any other string (a subsequence must preserve relative order, but you cannot embed a longer string into a shorter one).  
   - Thus, any unique string is automatically an uncommon subsequence.

3. **Choosing the longest**  
   - Among all unique strings, the longest length is the answer.  
   - If no unique string exists, answer is `-1`.

---

## 🤖 Edge Cases & Gotchas

| Edge Case | What to watch for | Fix |
|-----------|-------------------|-----|
| All strings identical | No uncommon subsequence | Return `-1` |
| Mixed lengths | Longer unique strings always dominate | No special handling needed |
| Empty string array (not allowed per constraints) | Still safe due to constraints | Not required |
| String lengths up to 10 | No risk of integer overflow | Use `int` safely |
| Very small array (`n = 2`) | Works fine | No changes |

---

## 🎯 Interview Tips

1. **Explain the observation early**: “If a string repeats, it can’t be uncommon. Therefore, we only need to look at unique strings.”
2. **Mention complexity**: O(n) time, O(n) space—fast and memory‑efficient.
3. **Show sample test cases**: Include the two Leetcode examples and a custom case like `["a","b","ab"]`.
4. **Discuss alternative (brute force)**: “We could generate all subsequences and test each, but that would be exponential and unnecessary.”

---

## 📝 Blog Article: “Master Leetcode 522 – Longest Uncommon Subsequence II (Java, Python, C++)”

---

### Title
**“Crack Leetcode 522: Longest Uncommon Subsequence II – 3 Winning Solutions (Java, Python, C++)”**

### Meta Description
Learn the optimal strategy for Leetcode 522 with clean Java, Python, and C++ code. Understand the problem, algorithm, edge cases, and how to impress recruiters. Perfect for coding interview prep.

### Keywords
- Leetcode 522
- Longest Uncommon Subsequence II
- Java solution
- Python solution
- C++ solution
- Coding interview
- Software engineer interview
- Algorithm interview
- Uncommon subsequence

### Outline

1. **Introduction** – why this problem matters.  
2. **Problem Statement** – clear, concise recap.  
3. **Observations** – the key insight that reduces the problem to a frequency count.  
4. **Algorithm** – step‑by‑step description.  
5. **Complexity Analysis** – time & space.  
6. **Java Implementation** – code + commentary.  
7. **Python Implementation** – code + commentary.  
8. **C++ Implementation** – code + commentary.  
9. **Edge Cases & Pitfalls** – how to avoid common mistakes.  
10. **Interview Tips** – how to present the solution during an interview.  
11. **Conclusion** – take‑away message & next steps.

---

### 1. Introduction

If you’re preparing for a software‑engineering interview, you’ll inevitably hit string‑heavy problems. Leetcode 522, *Longest Uncommon Subsequence II*, is a popular challenge because it tests both your conceptual thinking and your ability to write clean, efficient code. In this post we’ll walk through the problem, reveal the secret observation that turns an O(n²) nightmare into an O(n) solution, and then present polished implementations in **Java**, **Python**, and **C++**.

---

### 2. Problem Statement

> **Given** an array of strings `strs`, find the length of the longest string that is a subsequence of **exactly one** array element.  
> **Return** `-1` if no such string exists.

*Subsequence* means you can delete any number of characters while keeping the order of the remaining ones.

---

### 3. Observations

- A string that appears **more than once** can never be an uncommon subsequence (it’s a subsequence of itself and at least one other identical string).
- Any **unique** string cannot be a subsequence of another string in the array if it is longer than the other strings, because you cannot embed a longer string into a shorter one.
- Therefore, the problem reduces to: *Find the longest unique string in the array.*

This single line of insight collapses the problem from an exponential search of all subsequences to a simple frequency count.

---

### 4. Algorithm

1. **Count frequencies** of every string in `strs`.
2. **Iterate** over the original list; for each string that has frequency = 1, consider its length.
3. Return the **maximum length** among these unique strings; if none exist, return `-1`.

---

### 5. Complexity Analysis

| Operation | Complexity |
|-----------|------------|
| Counting frequencies | **O(n)** |
| Scanning for max | **O(n)** |
| Total | **O(n)** time, **O(n)** space |

With `n ≤ 50` and string lengths ≤ 10, this is comfortably fast for any interview environment.

---

### 6. Java Implementation

```java
import java.util.*;

public class Solution {
    public int findLUSlength(String[] strs) {
        Map<String, Integer> freq = new HashMap<>();
        for (String s : strs) {
            freq.put(s, freq.getOrDefault(s, 0) + 1);
        }

        int best = -1;
        for (String s : strs) {
            if (freq.get(s) == 1) {          // unique
                best = Math.max(best, s.length());
            }
        }
        return best;
    }
}
```

*Why it shines:* `HashMap` gives constant‑time lookups; we never generate subsequences.

---

### 7. Python Implementation

```python
from collections import Counter
from typing import List

class Solution:
    def findLUSlength(self, strs: List[str]) -> int:
        freq = Counter(strs)
        return max((len(s) for s in strs if freq[s] == 1), default=-1)
```

*Why it shines:* `Counter` and a generator expression keep the code concise and memory‑friendly.

---

### 8. C++ Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int findLUSlength(vector<string>& strs) {
        unordered_map<string, int> freq;
        for (const string& s : strs) ++freq[s];
        int best = -1;
        for (const string& s : strs)
            if (freq[s] == 1)
                best = max(best, (int)s.size());
        return best;
    }
};
```

*Why it shines:* `unordered_map` delivers the same constant‑time benefits, and the range‑based for loops read naturally.

---

### 9. Edge Cases & Pitfalls

| Edge Case | Fix |
|-----------|-----|
| All strings identical | The loop will never find a frequency = 1; return `-1`. |
| Mixed lengths with one longer unique string | The algorithm automatically picks it. |
| Empty string array (not allowed) | Constraints guarantee at least two strings. |
| Very short strings (`""`) | They count as subsequences but can’t beat longer unique strings; handled by length comparison. |

---

### 10. Interview Tips

1. **State the key observation first**: “If a string appears more than once, it can’t be uncommon.”
2. **Talk through the algorithm** while sketching the frequency map on a whiteboard.
3. **Mention complexity** upfront to reassure the interviewer.
4. **Show a quick test**: `["a","b","ab"] → 2` (the string `"ab"` is unique).
5. **Discuss alternative** (brute force) only if the interviewer asks, to show you understand why the optimized approach is superior.

---

### 11. Conclusion

Leetcode 522 is a deceptively simple problem once you spot the trick: the longest uncommon subsequence is simply the longest **unique** string. This reduces the task to a one‑pass frequency count and a max‑length scan, giving O(n) time and space. The Java, Python, and C++ solutions above are ready to copy‑paste into your coding interview toolkit. 

**Next step:** Practice explaining the insight aloud, and try solving Leetcode 521, *Longest Uncommon Subsequence*, where the strings are not necessarily unique and the brute force approach becomes unavoidable.

---

Happy coding, and good luck crushing your next interview! 🚀

--- 

End of Blog Article

--- 

These notes should help you prepare not just the code but the narrative that makes interviewers click “impressive.” Good luck!