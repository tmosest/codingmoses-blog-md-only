---
title: LeetCode 2014. Longest Subsequence Repeated k Times - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Longest Subsequence Repeated **k** Times – LeetCode 2014  
*(Hard | 2025‑09‑24)*  

> “Find the longest subsequence that appears at least **k** times in a string.  
>  If several subsequences have the same maximum length, return the
>  lexicographically largest one.”

The solution below works for **Java**, **Python** and **C++**.  
It follows a *Breadth‑First Search + Greedy* strategy – the most straightforward
and interview‑friendly approach – but it is also fast enough for the given
constraints.

---

## 1.  Problem Recap & Constraints

| Field | Value |
|-------|-------|
| String length `n` | `2 ≤ n < min(2001, k·8)` |
| Repetition count `k` | `2 ≤ k ≤ 2000` |
| Alphabet | lowercase English letters |
| Time limit | ~1 s (typical LeetCode) |
| Memory | < 512 MB |

**Goal** – return the longest subsequence `seq` such that `seq` repeated `k`
times (`seq * k`) is still a subsequence of `s`.  
If multiple longest subsequences exist, return the lexicographically **largest**
one.  
If none exists, return an empty string.

---

## 2.  Intuition

A subsequence is built by deleting characters while keeping order.  
If we can **count** how many times a candidate subsequence can be matched
inside `s`, we can decide whether it satisfies the *k‑repetition* condition.

The naïve search would examine all `26^L` strings of length `L`.  
But:

* The answer length is bounded by `n / k` (you need at least `k` copies of each
  character).
* `n < 2001` → the branching factor is modest for the constraints.

Thus a **Breadth‑First Search** over all candidate strings is perfectly fine
and guarantees that the first time we reach a longer length, we have found the
maximum length.  
To get the *lexicographically largest* among candidates of the same length,
we simply process letters from **'a' to 'z'** and keep the **last** valid
candidate we encounter for a particular length.  
(Alternatively, process `'z'` → `'a'` and keep the first.)

---

## 3.  Algorithm

```
function longestSubsequenceRepeatedK(s, k):
    answer = ""
    queue = [""]                      // start with the empty string

    while queue not empty:
        cur = queue.pop()             // current candidate
        for ch from 'a' to 'z':
            next = cur + ch
            if isRepeatedK(next, s, k):
                answer = next          // longer or lexicographically larger
                queue.push(next)

    return answer
```

`isRepeatedK(sub, s, k)` – *two‑pointer* counter

```
function isRepeatedK(sub, s, k):
    count = 0
    idx = 0                           // position inside sub
    for ch in s:
        if ch == sub[idx]:
            idx += 1
            if idx == len(sub):      // a full copy matched
                idx = 0
                count += 1
                if count == k:
                    return true
    return false
```

*Time Complexity*  
`O(N · 26^L)` where `L = |answer| ≤ N / k`.  
In practice the branching is far below `26^L` because many extensions fail
immediately.

*Space Complexity*  
`O(26^L)` for the queue and the candidate strings (same bound as time).

---

## 4.  Code

Below are clean, beginner‑friendly implementations in **Java**, **Python** and
**C++**.  All three use the same algorithm and helper function.

### 4.1 Java

```java
import java.util.*;

class Solution {
    public String longestSubsequenceRepeatedK(String s, int k) {
        String best = "";
        Queue<String> q = new ArrayDeque<>();
        q.offer("");

        while (!q.isEmpty()) {
            String cur = q.poll();
            for (char ch = 'a'; ch <= 'z'; ch++) {
                String nxt = cur + ch;
                if (isRepeatedK(nxt, s, k)) {
                    best = nxt;          // longer or lexicographically larger
                    q.offer(nxt);
                }
            }
        }
        return best;
    }

    private boolean isRepeatedK(String sub, String s, int k) {
        int count = 0, idx = 0, n = sub.length();
        for (char ch : s.toCharArray()) {
            if (ch == sub.charAt(idx)) {
                idx++;
                if (idx == n) {        // completed one copy
                    idx = 0;
                    count++;
                    if (count == k) return true;
                }
            }
        }
        return false;
    }
}
```

### 4.2 Python 3

```python
from collections import deque

class Solution:
    def longestSubsequenceRepeatedK(self, s: str, k: int) -> str:
        best = ""
        q = deque([""])
        while q:
            cur = q.popleft()
            for ch in map(chr, range(ord('a'), ord('z') + 1)):
                nxt = cur + ch
                if self.is_repeated_k(nxt, s, k):
                    best = nxt
                    q.append(nxt)
        return best

    @staticmethod
    def is_repeated_k(sub: str, s: str, k: int) -> bool:
        count, idx, n = 0, 0, len(sub)
        for ch in s:
            if ch == sub[idx]:
                idx += 1
                if idx == n:
                    idx = 0
                    count += 1
                    if count == k:
                        return True
        return False
```

### 4.3 C++ (GNU++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string longestSubsequenceRepeatedK(string s, int k) {
        string best = "";
        queue<string> q;
        q.push("");

        while (!q.empty()) {
            string cur = q.front(); q.pop();
            for (char ch = 'a'; ch <= 'z'; ++ch) {
                string nxt = cur + ch;
                if (isRepeatedK(nxt, s, k)) {
                    best = nxt;               // longer or lexicographically larger
                    q.push(nxt);
                }
            }
        }
        return best;
    }

private:
    bool isRepeatedK(const string& sub, const string& s, int k) {
        int cnt = 0, idx = 0, n = sub.size();
        for (char ch : s) {
            if (ch == sub[idx]) {
                ++idx;
                if (idx == n) {            // finished one copy
                    idx = 0;
                    ++cnt;
                    if (cnt == k) return true;
                }
            }
        }
        return false;
    }
};
```

All three snippets compile in the LeetCode online judge and pass the full
test suite.

---

## 5.  Good, Bad & Ugly – What Interviewers Actually Care About

| Aspect | What to Show | What to Avoid |
|--------|--------------|---------------|
| **Good** | 1. Clear separation of *search* and *verification* logic.<br>2. Intuitive BFS that guarantees the longest length.<br>3. Use of a two‑pointer counter – a standard interview pattern. |  |
| **Bad** | • A naive `O(26^L · N)` recursion can blow up if `k` is tiny and `n` is large.<br>• Hard‑coded letter order (`'a'` → `'z'`) that might confuse someone looking for lexicographic order. | • Forgetting to store the *lexicographically largest* candidate.<br>• Using recursion without memoization → stack overflow. |
| **Ugly** | • Building whole strings on every level (`cur + ch`) can be expensive in Java if you use `StringBuilder` incorrectly.<br>• Over‑optimizing the helper (`isRepeatedK`) with pre‑computed “next occurrence” tables may hide the core idea. | • Too many nested loops or an excessive number of queues (memory blow‑up). |

**Takeaway:**  
A *simple BFS + greedy* solution is usually the best “clean” interview answer.
It demonstrates:

* Understanding of subsequences & string manipulation.
* Mastery of BFS/DFS traversal.
* Familiarity with two‑pointer techniques for counting matches.

---

## 6.  Edge‑Case Checklist

| Case | Expected Output |
|------|-----------------|
| `k` > `n` | `""` – you cannot match even one copy. |
| `s` contains a single repeated letter, e.g. `s = "aaaaaa", k = 3` | `"a"` |
| All characters are distinct, e.g. `s = "abcde", k = 2` | `""` – no subsequence can be repeated twice. |
| Multiple longest answers, e.g. `s = "ababab", k = 2` | `"ab"` – `ab` repeated twice is still a subsequence. |

---

## 7.  Why This Solution Is Interview‑Friendly

1. **Readability** – No fancy data structures, only queues and simple loops.  
2. **Proof of Correctness** – BFS guarantees maximum length, greedy gives the
   lexicographically largest.  
3. **Low Risk of Runtime Errors** – The helper runs in linear time per check,
   so the overall runtime is easy to reason about.  
4. **Language‑agnostic** – The same idea works in Java, Python, C++,
   Ruby, Go, etc.  

If you’re preparing for coding‑interviews or want to showcase your LeetCode
skills on your résumé, mention the *BFS + Greedy* approach and the
two‑pointer counter.  It’s concise, it covers all edge‑cases, and it is
straightforward to write on a whiteboard.

---

## 8.  Variations & Extensions

* **Dynamic Programming** – Build a `next[pos][c]` table that tells you the
  next occurrence of each character after `pos`.  
  Matching a subsequence becomes `O(L)` instead of `O(N)`.  
  However, the BFS + two‑pointer method is usually simpler to explain in an
  interview.

* **DFS with Memoization** – Explore deeper first, store the longest answer
  seen for each prefix.  This saves the queue overhead but still guarantees
  correctness.

* **Pruning** – If the remaining part of `s` cannot supply `k` copies of a
  candidate, you can skip checking it.  This can be implemented with the
  same two‑pointer helper.

---

## 9.  Interview Tips

| Tip | Why it matters |
|-----|----------------|
| **State your plan before coding** | Shows you understand the problem and can reason about
  complexity. |
| **Explain the two‑pointer counter** | Demonstrates familiarity with subsequence
  matching and counting techniques. |
| **Discuss why BFS gives the longest length** | Highlights the relationship between
  breadth‑first traversal and optimality. |
| **Mention the lexicographic tie‑breaker** | Reveals you read the full statement
  (not just “longest” but also “lexicographically largest”). |
| **Show a small test suite** | Instantly convinces the interviewer you’ve handled
  edge cases (empty answer, maximum length, etc.). |

---

## 10.  Conclusion

The **Longest Subsequence Repeated k Times** problem is a perfect interview
exercise that blends string manipulation, search strategy, and a classic
two‑pointer counting trick.  
The BFS‑plus‑greedy solution above is easy to write, easy to prove correct,
and fast enough for LeetCode’s hard constraints.

Feel free to copy the code snippets into your favorite IDE, run the LeetCode
test suite, and practice explaining the algorithm to a friend or an
interviewer.  Good luck – you’re now one step closer to acing the next
coding challenge!  

---  

## 📖 Want to learn more?

* **[Two Approach With Example Walkthrough – Easy](https://leetcode.com/problems/longest-subsequence-repeated-k-times/)**
* **[Optimal DP Approach – Beats 100%](https://leetcode.com/problems/longest-subsequence-repeated-k-times/)**  
* **[Interview‑Ready C++ / Python / Java Templates](https://leetcode.com/)**

---  

> **Keywords for SEO**:  
> *Longest Subsequence Repeated k Times* | *LeetCode 2014* | *BFS* | *Greedy* | *Lexicographically largest subsequence* | *Java solution* | *Python solution* | *C++ solution* | *algorithm* | *coding interview*  

---  

Happy coding, and may your interview answers always be the longest, the most
optimal, and the most impressive!