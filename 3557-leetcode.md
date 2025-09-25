---
title: LeetCode 3557. Find Maximum Number of Non Intersecting Substrings - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap

**LeetCode 3557 – Find Maximum Number of Non‑Intersecting Substrings**

> Given a string `word`, return the maximum number of **non‑intersecting** substrings that  
>  * are at least 4 characters long, and  
>  * start and end with the same letter.

Examples  
```
word = "abcdeafdef"  →  2   ("abcdea", "fdef")
word = "bcdaaaab"    →  1   ("aaaa")
```

**Constraints**

* `1 ≤ word.length ≤ 2·10⁵`
* `word` contains only lowercase English letters.

---

## 2.  Two Elegant Solutions

| Approach | Time | Space | Why it works |
|----------|------|-------|--------------|
| **Dynamic Programming** | **O(n)** | **O(n)** | `dp[i]` = max count in `word[0…i‑1]`. For each position `i`, we look at the last occurrences of the same character and update `dp[i]` if the distance ≥ 4. |
| **Greedy (One‑Pass)** | **O(n)** | **O(1)** | Keep the first index of each letter seen since the last chosen substring. When the same letter appears again at distance ≥ 4, we take that substring, increment the answer, and reset everything. This works because any valid substring must end at the *first* possible position after the last taken substring. |

Both solutions are **linear**, but the greedy variant is more space‑efficient and perfectly suited for an interview.

Below you’ll find implementations in **Java**, **Python**, and **C++** for both approaches.

---

## 3.  Code

### 3.1  Dynamic Programming

```java
// Java – DP solution
class Solution {
    public int maxSubstrings(String word) {
        int n = word.length();
        int[] dp = new int[n + 1];                 // dp[i] = max count in word[0..i-1]
        List<Integer>[] pos = new ArrayList[26];   // positions of each letter

        for (int i = 0; i < 26; i++) pos[i] = new ArrayList<>();

        for (int i = 0; i < n; i++) {
            int c = word.charAt(i) - 'a';
            dp[i + 1] = dp[i];                     // skip current char

            for (int j : pos[c]) {                 // try all previous same‑letter positions
                if (i - j + 1 >= 4) {
                    dp[i + 1] = Math.max(dp[i + 1], dp[j] + 1);
                }
            }
            pos[c].add(i);                         // remember this occurrence
        }
        return dp[n];
    }
}
```

```python
# Python – DP solution
class Solution:
    def maxSubstrings(self, word: str) -> int:
        n = len(word)
        dp = [0] * (n + 1)
        pos = [[] for _ in range(26)]               # indices of each letter

        for i, ch in enumerate(word):
            idx = ord(ch) - 97
            dp[i + 1] = dp[i]                       # skip current position
            for j in pos[idx]:
                if i - j + 1 >= 4:
                    dp[i + 1] = max(dp[i + 1], dp[j] + 1)
            pos[idx].append(i)

        return dp[n]
```

```cpp
// C++ – DP solution
class Solution {
public:
    int maxSubstrings(string word) {
        int n = word.size();
        vector<int> dp(n + 1, 0);
        vector<vector<int>> pos(26);

        for (int i = 0; i < n; ++i) {
            int c = word[i] - 'a';
            dp[i + 1] = dp[i];
            for (int j : pos[c]) {
                if (i - j + 1 >= 4) {
                    dp[i + 1] = max(dp[i + 1], dp[j] + 1);
                }
            }
            pos[c].push_back(i);
        }
        return dp[n];
    }
};
```

### 3.2  Greedy (One‑Pass)

```java
// Java – Greedy solution
class Solution {
    public int maxSubstrings(String word) {
        int n = word.length();
        int[] first = new int[26];
        Arrays.fill(first, -1);
        int res = 0;

        for (int i = 0; i < n; ++i) {
            int c = word.charAt(i) - 'a';
            if (first[c] == -1) {
                first[c] = i;                       // remember first occurrence
            } else if (i - first[c] + 1 >= 4) {      // valid substring found
                ++res;
                Arrays.fill(first, -1);              // reset for next block
            }
        }
        return res;
    }
}
```

```python
# Python – Greedy solution
class Solution:
    def maxSubstrings(self, word: str) -> int:
        first = [-1] * 26
        res = 0
        for i, ch in enumerate(word):
            idx = ord(ch) - 97
            if first[idx] == -1:
                first[idx] = i
            elif i - first[idx] + 1 >= 4:
                res += 1
                first = [-1] * 26   # reset for the next segment
        return res
```

```cpp
// C++ – Greedy solution
class Solution {
public:
    int maxSubstrings(string word) {
        int n = word.size();
        vector<int> first(26, -1);
        int res = 0;

        for (int i = 0; i < n; ++i) {
            int c = word[i] - 'a';
            if (first[c] == -1) {
                first[c] = i;
            } else if (i - first[c] + 1 >= 4) {
                ++res;
                first.assign(26, -1);               // reset
            }
        }
        return res;
    }
};
```

---

## 4.  Blog Post – “The Good, The Bad, and The Ugly of LeetCode 3557”

> **Title:** *Mastering LeetCode 3557 – The Good, The Bad, and The Ugly of Non‑Intersecting Substrings*  
> **Keywords:** LeetCode 3557, Maximum Number of Non‑Intersecting Substrings, Java Python C++ solution, dynamic programming, greedy algorithm, coding interview, job interview coding problem, interview prep

---

### 4.1  Introduction

When preparing for a software‑engineering interview, you’ll encounter a handful of problems that recur across companies: sliding windows, two‑pointers, graph traversals, and, of course, **LeetCode 3557 – Find Maximum Number of Non‑Intersecting Substrings**.  
At first glance, the problem looks like a classic “interval scheduling” exercise. But a subtle requirement – each substring must start and end with the same character and be at least 4 characters long – forces you to think about *letter positions* rather than just intervals.

Below we walk through the **good**, **bad**, and **ugly** aspects of this problem, explain why a greedy solution works, and give you ready‑to‑copy code in Java, Python, and C++.

---

### 4.2  The Good – What Makes This Problem Great for Interviews

| Reason | Why It Matters |
|--------|----------------|
| **Linear Time** | Both the DP and greedy solutions run in O(n) – perfect for the interview’s time limits. |
| **Space Variability** | You can choose a DP solution (O(n) space) or a greedy one (O(1) space). Demonstrating awareness of space trade‑offs is a plus. |
| **Clear State Machine** | The greedy approach reduces the problem to “remember the first occurrence of each letter since the last chosen substring.” This is a clean, intuitive state that’s easy to explain. |
| **No External Libraries** | The problem can be solved with just arrays and loops – you don’t need a fancy library. |
| **Real‑World Analog** | It’s a miniature version of *non‑overlapping resource allocation*, a common theme in real systems. |

> **Takeaway:** Interviewers love problems that let you showcase a clean algorithmic insight (here: *first‑occurrence + reset*).

---

### 4.3  The Bad – Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| **Over‑Counting** | If you simply count every “matching pair” you’ll double‑count overlapping substrings. Always ensure the new substring starts *after* the last chosen one. |
| **Wrong Length Check** | The length condition is *≥ 4*, not “strictly greater”. A mistake here can lead to wrong answers on edge cases like “aaaa”. |
| **Using HashMap Instead of Fixed Array** | Because the alphabet is only 26 letters, a fixed array (`int[26]`) is both faster and less error‑prone than a hashmap. |
| **Ignoring the Reset** | In the greedy solution, forgetting to reset the `first` array after picking a substring will allow future substrings to cross the boundary. |
| **Indexing Bugs** | `dp[i]` refers to the prefix ending *before* `i`. Mixing up `i` and `i+1` is a classic off‑by‑one bug. |

> **Pro Tip:** Write a few test cases by hand before coding. Verify that “abcdea” is accepted but “abcda” is not.

---

### 4.4  The Ugly – Why a Straight‑Forward Interval Scheduling Doesn’t Work

Many candidates mistakenly model each potential substring as an interval `[start, end]` and then apply the classic “pick the earliest finishing interval”. The catch is:

*An interval’s *end* is not simply `j` – it must satisfy the *matching character* and *minimum length* constraints.*

If you ignore the “first‑occurrence” requirement, you’ll mistakenly think you can pick a longer substring later to get more substrings overall. In fact, picking a longer one can only *decrease* your final count because you lose more positions that could start another valid substring.

The beauty of the greedy solution is that, given you’re scanning left‑to‑right, **the earliest possible end after the last pick is always optimal**. Any later end would only skip potential matches for future letters.

---

### 4.4  Why the Greedy Pass Works – The State‑Machine Proof

1. **State:** `first[26]` – the index of the first occurrence of each letter seen *since the last accepted substring*.  
   If a letter hasn’t appeared, its entry is `-1`.

2. **Transition:**  
   * When we see a new letter, we store its index (`first[c] = i`).  
   * When the same letter reappears at index `j`, if `j - first[c] + 1 ≥ 4` we *must* take the substring `[first[c], j]`.  
     *Why?* Because any valid substring that starts after `first[c]` and ends at or before `j` would necessarily overlap with `[first[c], j]` (the current one) or start before `first[c]` (which would be illegal).  
   * After taking it, we reset `first` to `-1` for all letters – this enforces the non‑intersecting requirement.

3. **Termination:** We finish the loop with the maximum possible number of substrings because every time we’re forced to reset we have already taken the *earliest* feasible substring.

This greedy proof is short, elegant, and perfect for an interview explanation.

---

### 4.5  Ready‑to‑Copy Solutions

(See Section 3 for code in Java, Python, and C++.)

---

### 4.6  Final Thoughts – How to Use This in Your Prep Routine

1. **Run the Code** – copy the greedy solution into your editor, run on the provided examples, then create your own corner cases (`"aaaa"`, `"abcdabcdabcd"`, `"ababab"`).  
2. **Explain the State** – During the interview, say: *“I’m tracking the first time each letter appears after the last chosen substring; when the same letter shows up again at distance ≥ 4 I finalize that substring and clear the state.”*  
3. **Mention DP** – If time permits, show you know the DP variant: “I could also keep an array of best counts for each prefix, but that uses O(n) memory.”  
4. **Ask About Constraints** – “If the string could contain Unicode, would we still keep a fixed array?” – this demonstrates deeper thinking.

---

### 4.7  Wrap‑Up

LeetCode 3557 may look intimidating at first, but the greedy trick of *first occurrence + reset* turns it into a lightning‑fast interview question.  
Use the code snippets above as a reference, practice explaining the algorithm in plain English, and you’ll be ready to crush this problem (and impress your future employer) on day one.  

Happy coding! 🚀

--- 

### 4.8  References

* [LeetCode 3557 – Find Maximum Number of Non‑Intersecting Substrings](https://leetcode.com/problems/find-maximum-number-of-non-intersecting-substrings/)
* [Interview‑Prep – Greedy vs. DP](https://leetcode.com/articles/interview-preparation/)

---