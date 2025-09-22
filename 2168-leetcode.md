---
title: LeetCode 2168. Unique Substrings With Equal Digit Frequency - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Code – Three Languages

Below are **ready‑to‑copy** solutions for LeetCode 2168 – *Unique Substrings With Equal Digit Frequency* – written in **Java**, **Python**, and **C++**.  
All three follow the same O(N² · 10) idea:  
* enumerate all substrings,  
* maintain a 10‑element frequency array while extending the substring,  
* test whether every non‑zero count is equal,  
* insert the substring into a hash‑based set to keep only unique ones.

```text
LeetCode 2168
Unique Substrings With Equal Digit Frequency
Time   :  O(N² · 10)
Space  :  O(N²)   (set of unique substrings)
```

---

### Java – 100% Java 17

```java
import java.util.*;

public class Solution {
    // Check whether all non‑zero counts are the same
    private boolean isValid(int[] cnt) {
        int pivot = -1;
        for (int i = 0; i < 10; i++) {
            if (cnt[i] == 0) continue;
            if (pivot == -1) pivot = cnt[i];
            else if (cnt[i] != pivot) return false;
        }
        return true;
    }

    public int equalDigitFrequency(String s) {
        Set<String> unique = new HashSet<>();
        int n = s.length();

        for (int i = 0; i < n; i++) {
            int[] cnt = new int[10];
            StringBuilder cur = new StringBuilder();

            for (int j = i; j < n; j++) {
                int d = s.charAt(j) - '0';
                cnt[d]++;

                cur.append(s.charAt(j));

                if (isValid(cnt)) unique.add(cur.toString());
            }
        }
        return unique.size();
    }
}
```

> **Tip:** The `StringBuilder` is reused for each `i`, so we avoid constructing a new string for every character.  
> The `isValid` method scans only 10 positions – negligible overhead.

---

### Python – 3.10+

```python
from typing import List, Set

class Solution:
    def equalDigitFrequency(self, s: str) -> int:
        n = len(s)
        unique: Set[str] = set()

        for i in range(n):
            cnt = [0] * 10
            cur = []

            for j in range(i, n):
                d = ord(s[j]) - 48
                cnt[d] += 1
                cur.append(s[j])

                # Test equality of all non‑zero counts
                pivot = None
                ok = True
                for c in cnt:
                    if c == 0: continue
                    if pivot is None:
                        pivot = c
                    elif c != pivot:
                        ok = False
                        break
                if ok:
                    unique.add(''.join(cur))

        return len(unique)
```

> **Why a list for `cur`?**  
> `''.join(cur)` is O(len(cur)), but building the list once per `i` keeps the inner loop linear.

---

### C++ – Modern C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    bool isValid(const array<int,10>& cnt) {
        int pivot = -1;
        for (int c : cnt) {
            if (c == 0) continue;
            if (pivot == -1) pivot = c;
            else if (c != pivot) return false;
        }
        return true;
    }

    int equalDigitFrequency(string s) {
        unordered_set<string> uniq;
        int n = s.size();

        for (int i = 0; i < n; ++i) {
            array<int,10> cnt{};            // all zeros
            string cur;

            for (int j = i; j < n; ++j) {
                int d = s[j] - '0';
                ++cnt[d];
                cur.push_back(s[j]);

                if (isValid(cnt)) uniq.insert(cur);
            }
        }
        return static_cast<int>(uniq.size());
    }
};
```

> **Why `unordered_set<string>`?**  
> The built‑in hashing is fast enough for ≤ 1 000 substrings.  
> Using `std::string` keeps the code readable; the overhead of copying is trivial for the given constraints.

---

## 2.  Blog Article – “The Good, The Bad, and The Ugly of LeetCode 2168”

> **Meta Title**  
> 2168 LeetCode: Unique Substrings with Equal Digit Frequency – Java, Python & C++ Solutions  
> **Meta Description**  
> Master LeetCode 2168 with clean Java, Python, and C++ solutions. Learn the algorithm, pitfalls, and how to impress hiring managers with your coding style.

---

### Introduction

When recruiters ask you to solve *Unique Substrings With Equal Digit Frequency* (LeetCode 2168), they’re looking for three things:

1. **Correctness** – does the algorithm find every qualifying substring?  
2. **Efficiency** – can it handle the worst‑case input within time limits?  
3. **Clean code** – is the solution readable and maintainable?

In this article we walk through the **good**, the **bad**, and the **ugly** parts of typical solutions, show a production‑ready implementation in three languages, and point out interview‑friendly improvements.

---

### Problem Recap

> **Input** – a string `s` (length ≤ 1000) consisting only of decimal digits.  
> **Goal** – count **unique** substrings where *every digit that appears* does so the **same number of times**.

> **Examples**  
> *“1212”* → `{"1","2","12","21","1212"}` → answer = 5  
> *“12321”* → 9 unique substrings

The key phrase is *“every digit that appears”*. Digits that do not appear at all are ignored.

---

### The “Good” – O(N² · 10) Brute‑Force with Early Validation

Because `s.length ≤ 1000`, a quadratic algorithm is acceptable. The trick is to **avoid re‑scanning** the substring on each extension:

1. For each start index `i`, initialise a 10‑element frequency array `cnt` and an empty `StringBuilder` (Java) / list (Python) / string (C++).
2. Grow the substring one character at a time (`j` from `i` to `n-1`), updating `cnt` and the current string.
3. After each extension, run `isValid(cnt)` – a single pass over 10 elements – to check that all non‑zero frequencies are equal.
4. If valid, insert the substring into a hash set to keep uniqueness.

**Why is this good?**

- **Linear per substring** – the inner loop does O(1) work (10 steps).  
- **No heavy string copying** – we keep a running string and only materialise the key for the set when needed.  
- **Simple, idiomatic code** – easy for recruiters to read and reason about.

---

### The “Bad” – Rolling Hashes, Trie, or Extra Data Structures

Some solutions on LeetCode add complexity for little gain:

| Approach | Overkill? | Why? |
|----------|-----------|------|
| Rolling hash + double‑ended queue | Yes | The hash would need to account for 10 counters, not just a substring. |
| Trie of substrings | Yes | Building a trie of up to ½ N² nodes is unnecessary for N = 1000. |
| Pre‑computing all pair frequencies | Yes | Still O(N²) but wastes memory and time. |

**Takeaway:** In interviews, a *clear* O(N²) solution beats a clever but unreadable optimization. Recruiters value **understanding** over *cleverness*.

---

### The “Ugly” – Frequent Substring Reconstruction

A common mistake is rebuilding the substring on each iteration:

```java
for (int j = i; j < n; j++) {
    // ...
    String sub = s.substring(i, j + 1);   // O(length)
    if (isValid(cnt)) set.add(sub);
}
```

This adds an extra `O(L)` factor (where `L` is the substring length). For a 1000‑character string, it can push you toward the 10‑second limit on some judges.  

**Fix:** Keep a mutable string (`StringBuilder`, `String`, or list) and *only* materialise it when you know it’s valid.

---

### Step‑by‑Step Code Walk‑through

Let’s illustrate the Java version, which is the most verbose yet clear.

```java
public int equalDigitFrequency(String s) {
    Set<String> unique = new HashSet<>();
    int n = s.length();

    for (int i = 0; i < n; i++) {
        int[] cnt = new int[10];
        StringBuilder cur = new StringBuilder();

        for (int j = i; j < n; j++) {
            int d = s.charAt(j) - '0';
            cnt[d]++;

            cur.append(s.charAt(j));

            if (isValid(cnt)) unique.add(cur.toString());
        }
    }
    return unique.size();
}
```

* **Outer loop** – fixes the start index `i`.  
* **Inner loop** – expands the substring to the right.  
* **`cnt`** – updates frequencies in O(1).  
* **`isValid`** – only 10 steps, so overall time is `O(N²)`.

The Python and C++ variants follow the same logic; just adjust syntax and data types.

---

### Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Enumerating substrings | `O(n²)` | – |
| Updating frequencies | `O(1)` per step | – |
| Validity check | `O(10)` per step | – |
| Hash set | up to `O(n²)` unique substrings | – |
| **Total** | `O(n²)` | `O(n²)` |

With `n ≤ 1000`, the worst‑case operations are roughly `10⁶` – comfortably within the 2‑second limit on most platforms.

---

### Interview‑Friendly Tips

| Issue | Fix |
|-------|-----|
| **Unnecessary string copies** | Use a mutable builder / list; only `join`/`toString` on valid substrings. |
| **Ignoring zero counts** | In `isValid`, skip entries where `cnt[i] == 0`. |
| **Set overhead** | Use `HashSet` (Java), `unordered_set` (C++), or `set` (Python). |
| **Readability** | Keep function names descriptive (`equalDigitFrequency`, `isValid`). |
| **Edge cases** | Test single‑character strings, all same digits, alternating digits. |

---

### What Recruiters Look For

1. **Correctness** – handle all edge cases, no off‑by‑ones.  
2. **Time‑space trade‑off** – explain why `O(n²)` is safe here.  
3. **Clean coding style** – avoid unnecessary abstractions.  
4. **Optional** – mention possible improvements (e.g., using bitmasks for 10 digits) but explain why they’re unnecessary for the constraints.

---

### Closing Thoughts

LeetCode 2168 is a great problem to showcase a **balanced approach**: a straight‑forward algorithm that is both **efficient** for the given limits and **easily understandable**.  
By mastering the Java, Python, and C++ implementations above, you’ll be ready to:

* Discuss the algorithm on a whiteboard.  
* Write clean, bug‑free code in an interview.  
* Show recruiters that you prioritize **clarity over cleverness**—a key trait for senior developers.

Happy coding, and good luck landing that next role!

---