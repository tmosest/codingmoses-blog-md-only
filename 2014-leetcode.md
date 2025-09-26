---
title: LeetCode 2014. Longest Subsequence Repeated k Times - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # The Longest Subsequence Repeated *k* Times – A Job‑Ready Blog Post  
*(LeetCode 2014 – Hard, “Longest Subsequence Repeated k Times”)*  

---

## 1️⃣ Why This Problem Matters

- **Interview‑buzzword** – LeetCode Hard problems are a gold‑mine for data‑structure & algorithm interviews.  
- **Real‑world relevance** – Subsequence matching is at the heart of DNA sequencing, text mining, and compression.  
- **Show‑case** – Solving it in **Java, Python and C++** demonstrates mastery of language‑specific tricks (streams, iterators, fast I/O).

If you can explain the idea, complexity and present clean code in three major languages, you’ll be a strong candidate for roles that ask you to write “clever, production‑ready code”.

---

## 2️⃣ Problem Recap

> **Input**  
> - `s` – a string of lowercase English letters (`2 ≤ |s| < min(2001, k·8)`)  
> - `k` – the number of repetitions (`2 ≤ k ≤ 2000`)  
> **Output**  
> The longest subsequence `seq` such that `seq` repeated `k` times (`seq·k`) is still a subsequence of `s`.  
> If several subsequences share the maximum length, return the lexicographically largest one. Return `""` if none exist.

Examples  
| `s` | `k` | result |
|------|-----|--------|
| `letsleetcode` | 2 | `let` |
| `bb` | 2 | `b` |
| `ab` | 2 | `""` |

---

## 3️⃣ The Core Insight

> **A subsequence `seq` repeats `k` times iff we can find `k` non‑overlapping copies of `seq` in order.**  
> In other words, walking once through `s` we should be able to “consume” the characters of `seq` `k` times.

This gives us a linear‑time helper:

```text
count = 0, i = 0          // i indexes seq
for ch in s:
    if ch == seq[i]:
        i += 1
        if i == len(seq):          // one copy finished
            i = 0
            count += 1
            if count == k: return True
return False
```

The helper works for *any* `seq` in `O(|s|)` time.

---

## 4️⃣ Brute‑Force vs. Optimised Search

- **Brute‑force** – Enumerate all subsequences (`2^n`) → impossible.  
- **Dynamic Programming** – Too many states (`26^L`) if we store every possible subsequence.  
- **Breadth‑First Search (BFS) with Greedy Ordering** – Explore subsequences level‑by‑level, always extending with `a…z`.  
  - The **last** valid subsequence of a given length will be the lexicographically largest (since we try letters in ascending order).  
  - We stop when the queue becomes empty; the answer is the longest valid one we have seen.

With the constraints (`|s| < 2001, k ≤ 2000, |s| < k·8`), the maximal possible subsequence length is `|s| / k` (≈ 8).  
Thus the BFS explores at most `26^8 ≈ 2·10^11` strings *in theory*, but in practice the `isK` check cuts the search drastically.

---

## 5️⃣ The Algorithm (Greedy BFS)

1. **Initialize**  
   - `queue` ← `[""]` (empty subsequence).  
   - `answer` ← `""`.

2. **While queue not empty**  
   - Pop `curr`.  
   - For each `c` in `'a'` … `'z'`:  
     - `next = curr + c`.  
     - If `isK(next, s, k)` is true:  
       - Update `answer = next` (longest found so far).  
       - Push `next` into the queue.

3. **Return** `answer`.

> **Why greedy?**  
> Because we iterate letters in natural order, the *last* valid subsequence of a given length is automatically the lexicographically largest.  
> **Why BFS?**  
> It guarantees we finish exploring all subsequences of length `L` before moving to `L+1`, so we naturally discover the longest one.

---

## 6️⃣ Complexity Analysis

| Aspect | Estimate |
|--------|----------|
| `isK` time | `O(|s|)` |
| Max levels | `⌊|s| / k⌋` (≈ 8) |
| Total visited strings | `≤ 26^{|s|/k}` but practically far less. |
| **Time** | `O( |s| · visited )` – in practice a few milliseconds. |
| **Space** | `O( visited · average_length )` – also tiny for given limits. |

---

## 7️⃣ The Good, The Bad, and The Ugly

| Category | Good | Bad | Ugly |
|----------|------|-----|------|
| **Concept** | Clear linear‑time helper, intuitive greedy BFS | BFS can explode combinatorially in pathological cases | None – the search space is naturally bounded by constraints |
| **Code** | Clean, short, uses only standard library | Requires manual string concatenation which can be slower in Python | None – no external dependencies |
| **Readability** | Inline comments, step‑by‑step | `isK` name may be confusing – “canRepeatedK?” | None |

---

## 8️⃣ SEO‑Optimised Takeaway

- **Title**: “LeetCode 2014 – Longest Subsequence Repeated k Times (Java/Python/C++ Solutions)”
- **Meta‑Description**: “Master the Hard LeetCode problem ‘Longest Subsequence Repeated k Times’ with step‑by‑step BFS and greedy solutions in Java, Python, and C++. Learn time‑complexity, code snippets, and interview‑friendly explanations.”
- **Keywords**: LeetCode Hard, Longest Subsequence, Repeated k Times, Interview Problem, BFS, Greedy, Java, Python, C++, Algorithm, Data Structures.

---

## 9️⃣ Full Code (Three Languages)

> **Note** – All three solutions use the same algorithmic idea but adapt to language idioms.  
> They are ready to paste into LeetCode’s editor.

---

### 9.1 Java 17

```java
import java.util.*;

public class Solution {
    // Main entry
    public String longestSubsequenceRepeatedK(String s, int k) {
        String best = "";
        Queue<String> q = new LinkedList<>();
        q.add("");          // start with empty subsequence

        while (!q.isEmpty()) {
            String cur = q.poll();
            for (char ch = 'a'; ch <= 'z'; ch++) {
                String nxt = cur + ch;
                if (isK(nxt, s, k)) {
                    best = nxt;        // longest found so far
                    q.offer(nxt);      // explore extensions
                }
            }
        }
        return best;
    }

    // Check if nxt repeated k times is a subsequence of s
    private boolean isK(String nxt, String s, int k) {
        int idx = 0, count = 0;          // idx -> position in nxt
        for (int i = 0; i < s.length(); i++) {
            if (s.charAt(i) == nxt.charAt(idx)) {
                idx++;
                if (idx == nxt.length()) { // one copy finished
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

---

### 9.2 Python 3.10

```python
from collections import deque

class Solution:
    def longestSubsequenceRepeatedK(self, s: str, k: int) -> str:
        best = ""
        q = deque([""])                 # BFS queue

        while q:
            cur = q.popleft()
            for ch in "abcdefghijklmnopqrstuvwxyz":
                nxt = cur + ch
                if self.is_k(nxt, s, k):
                    best = nxt
                    q.append(nxt)
        return best

    @staticmethod
    def is_k(nxt: str, s: str, k: int) -> bool:
        idx, count = 0, 0
        for ch in s:
            if ch == nxt[idx]:
                idx += 1
                if idx == len(nxt):     # one copy finished
                    idx = 0
                    count += 1
                    if count == k:
                        return True
        return False
```

> *Python string concatenation is fine for the short strings produced by the BFS.*

---

### 9.3 C++ (17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string longestSubsequenceRepeatedK(string s, int k) {
        string best = "";
        queue<string> q;
        q.push("");                    // start from empty subsequence

        while (!q.empty()) {
            string cur = q.front(); q.pop();
            for (char ch = 'a'; ch <= 'z'; ++ch) {
                string nxt = cur + ch;
                if (isK(nxt, s, k)) {
                    best = nxt;            // longest so far
                    q.push(nxt);           // explore deeper
                }
            }
        }
        return best;
    }

private:
    bool isK(const string& nxt, const string& s, int k) {
        int idx = 0, cnt = 0;          // idx -> position in nxt
        for (char c : s) {
            if (c == nxt[idx]) {
                ++idx;
                if (idx == (int)nxt.size()) {   // copy finished
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

> *The use of `queue<string>` and simple loops keeps the code concise and fast.*

---

## 🔚 Final Thoughts

- **Show your ability** to translate a problem statement into a clear algorithmic strategy.  
- **Mention the helper** `isK` – it’s the “magic” that makes the whole solution linear in `|s|`.  
- **Discuss complexity** – interviewers love seeing you talk about time & space.  
- **Provide all three languages** – demonstrates versatility and readiness for a diverse tech stack.

Good luck cracking the problem and landing that next interview! 🚀