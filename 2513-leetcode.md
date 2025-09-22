---
title: LeetCode 2513. Minimize the Maximum of Two Arrays - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Problem Recap – Minimize the Maximum of Two Arrays  

You are given four integers:

| Variable | Meaning |
|----------|---------|
| **divisor1** | `arr1` cannot contain any number divisible by this value |
| **divisor2** | `arr2` cannot contain any number divisible by this value |
| **uniqueCnt1** | `arr1` must contain exactly this many *distinct* positive integers |
| **uniqueCnt2** | `arr2` must contain exactly this many *distinct* positive integers |

The two arrays must be disjoint (no number can appear in both).  
Your task: **choose the integers** for both arrays so that the *maximum* integer used in either array is as small as possible.  
Return that minimum achievable maximum.

The constraints are huge (`uniqueCnt1`, `uniqueCnt2` up to 10⁹) – a brute‑force solution is impossible.

---

## 2. Intuition

The problem is a typical *“binary search on the answer”* question.

* If you fix an upper bound `M`, you can decide whether it is **possible** to fill the two arrays with all numbers `≤ M`.  
* If it is possible, you can try a smaller `M`; if it is impossible, you must try a larger one.  
* Binary search on `M` gives the minimal feasible maximum in `O(log range)` steps.

So the key sub‑problem is:  

> **Given a limit `M`, can we select `uniqueCnt1` numbers not divisible by `divisor1`,  
>  `uniqueCnt2` numbers not divisible by `divisor2`, all ≤ `M`, and all distinct?**

The answer can be derived from simple counting:

* `cntBoth` – numbers ≤ `M` divisible by **both** divisors  
* `cntOnly1` – divisible only by `divisor1` (not by `divisor2`)  
* `cntOnly2` – divisible only by `divisor2` (not by `divisor1`)  
* `cntOther` – numbers divisible by **neither** divisor  

Only `cntOther` + `cntOnly2` are candidates for `arr1`.  
Only `cntOther` + `cntOnly1` are candidates for `arr2`.

If we try to give `arr1` all the numbers it can use and then check whether
`arr2` still has enough, the decision reduces to a handful of arithmetic
operations – no need for an actual greedy construction.

---

## 3. Algorithm (Pseudo‑code)

```
function possible(M):
    l = lcm(divisor1, divisor2)
    both    = M / l
    only1   = M / divisor1 - both
    only2   = M / divisor2 - both
    other   = M - (both + only1 + only2)

    # Numbers that can be used by arr1:
    avail1 = other + only2
    # Numbers that can be used by arr2:
    avail2 = other + only1

    if uniqueCnt1 > avail1 or uniqueCnt2 > avail2:
        return false

    # After giving arr1 and arr2 all exclusive candidates,
    # how many "other" numbers are left?
    usedOther = max(0, uniqueCnt1 - only2) + max(0, uniqueCnt2 - only1)
    remainingOther = other - usedOther

    return remainingOther >= 0
```

Binary search around `M`:

```
low = 1
high = 2 * (uniqueCnt1 + uniqueCnt2) * max(divisor1, divisor2)   # safe upper bound
while low < high:
    mid = (low + high) // 2
    if possible(mid):
        high = mid
    else:
        low = mid + 1
return low
```

All arithmetic is done with 64‑bit integers (`long` in Java, `long long` in C++,
`int`‑type in Python 3 because it auto‑promotes).

---

## 4. Complexity

| Step | Time | Memory |
|------|------|--------|
| Binary search | `O(log range)` ≈ `O(log 10¹⁴)` ( < 50 iterations) | `O(1)` |
| `possible` | `O(1)` | `O(1)` |

Total: **`O(log range)` time, `O(1)` memory** – easily fast enough for the limits.

---

## 5. Implementation

Below are clean, production‑ready implementations in **Java, Python, and C++**.

---

### 5.1 Java (Java 17)

```java
import java.io.*;
import java.util.*;

public class Solution {
    public int minimizeSet(int divisor1, int divisor2, int uniqueCnt1, int uniqueCnt2) {
        long d1 = divisor1, d2 = divisor2;
        long c1 = uniqueCnt1, c2 = uniqueCnt2;

        long low = 1;
        long high = 2L * (c1 + c2) * Math.max(d1, d2);   // safe upper bound

        while (low < high) {
            long mid = low + (high - low) / 2;
            if (possible(mid, d1, d2, c1, c2)) {
                high = mid;
            } else {
                low = mid + 1;
            }
        }
        return (int) low;
    }

    private boolean possible(long m, long d1, long d2, long c1, long c2) {
        long l = lcm(d1, d2);

        long both   = m / l;
        long only1  = m / d1 - both;
        long only2  = m / d2 - both;
        long other  = m - (both + only1 + only2);

        long avail1 = other + only2;   // arr1 candidates
        long avail2 = other + only1;   // arr2 candidates

        if (c1 > avail1 || c2 > avail2) return false;

        long usedOther = Math.max(0, c1 - only2) + Math.max(0, c2 - only1);
        long remainingOther = other - usedOther;

        return remainingOther >= 0;
    }

    private long lcm(long a, long b) {
        return a / gcd(a, b) * b;          // a/gcd * b never overflows (64 bit)
    }

    private long gcd(long a, long b) {
        while (b != 0) {
            long t = a % b;
            a = b;
            b = t;
        }
        return a;
    }

    // ------------------------------------------------------------
    // Main method to read input for the online judge.
    // ------------------------------------------------------------
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer st = new StringTokenizer(br.readLine());
        int d1 = Integer.parseInt(st.nextToken());
        int d2 = Integer.parseInt(st.nextToken());
        int c1 = Integer.parseInt(st.nextToken());
        int c2 = Integer.parseInt(st.nextToken());
        Solution s = new Solution();
        System.out.println(s.minimizeSet(d1, d2, c1, c2));
    }
}
```

---

### 5.2 Python 3 (3.10+)

```python
import sys
import math

def minimizeSet(divisor1: int, divisor2: int,
                uniqueCnt1: int, uniqueCnt2: int) -> int:
    d1, d2 = divisor1, divisor2
    c1, c2 = uniqueCnt1, uniqueCnt2

    low, high = 1, 2 * (c1 + c2) * max(d1, d2)

    while low < high:
        mid = (low + high) // 2
        if possible(mid, d1, d2, c1, c2):
            high = mid
        else:
            low = mid + 1
    return low


def possible(m: int, d1: int, d2: int, c1: int, c2: int) -> bool:
    l = lcm(d1, d2)
    both = m // l
    only1 = m // d1 - both
    only2 = m // d2 - both
    other = m - (both + only1 + only2)

    avail1 = other + only2
    avail2 = other + only1
    if c1 > avail1 or c2 > avail2:
        return False

    used_other = max(0, c1 - only2) + max(0, c2 - only1)
    return other - used_other >= 0


def lcm(a: int, b: int) -> int:
    return a // math.gcd(a, b) * b


# ------------------------------------------------------------------
# Standard online‑judge entry point
# ------------------------------------------------------------------
if __name__ == "__main__":
    data = list(map(int, sys.stdin.read().strip().split()))
    d1, d2, c1, c2 = data
    print(minimizeSet(d1, d2, c1, c2))
```

---

### 5.3 C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minimizeSet(int divisor1, int divisor2, int uniqueCnt1, int uniqueCnt2) {
        long long d1 = divisor1, d2 = divisor2;
        long long c1 = uniqueCnt1, c2 = uniqueCnt2;

        long long low = 1;
        long long high = 2LL * (c1 + c2) * max(d1, d2);   // safe upper bound

        while (low < high) {
            long long mid = low + (high - low) / 2;
            if (possible(mid, d1, d2, c1, c2)) {
                high = mid;
            } else {
                low = mid + 1;
            }
        }
        return static_cast<int>(low);
    }

private:
    static long long lcm(long long a, long long b) {
        return a / std::gcd(a, b) * b;   // no overflow: 64‑bit
    }

    static bool possible(long long m,
                         long long d1, long long d2,
                         long long c1, long long c2) {
        long long l = lcm(d1, d2);
        long long both  = m / l;
        long long only1 = m / d1 - both;
        long long only2 = m / d2 - both;
        long long other = m - (both + only1 + only2);

        long long avail1 = other + only2;
        long long avail2 = other + only1;
        if (c1 > avail1 || c2 > avail2) return false;

        long long usedOther = max(0LL, c1 - only2) + max(0LL, c2 - only1);
        long long remainingOther = other - usedOther;
        return remainingOther >= 0;
    }
};
```

---

## 6. “Good, Bad, Ugly” – What to Watch Out For

| Aspect | What to do | What *not* to do |
|--------|-----------|------------------|
| **Good** | Use 64‑bit arithmetic everywhere – the answer can be ~10¹⁴. | Stick to 32‑bit `int` – you’ll get wrong answers for large inputs. |
| **Good** | Binary search the *upper bound* conservatively (`high = 2*(c1+c2)*max(d1,d2)`); you’ll never hit the “search too high” case. | Pick a too small `high` (e.g., `c1+c2`) – the binary search will loop forever or return an incorrect result. |
| **Good** | Test the helper with a small helper script first (`M=10,20,30` etc.). | Assume the “available‑only‑exclusive” check is enough – you still need to account for the shared `other` pool. |
| **Bad** | Forget that `cntOther` counts *neither* divisor. | Think that `cntOther` counts “both” – then you’ll be able to fill `arr1` but not `arr2`. |
| **Ugly** | A “greedy pick” implementation that actually builds two arrays will quickly blow up on the `10⁹` constraints and is unnecessary. | Implement a naïve back‑tracking or two‑pointer greedy – it will time out. |

---

## 7. Why This Problem Rocks for a Coding Interview

1. **Shows your mastery of “binary search on answer”** – a recurring interview pattern.
2. **Tests your ability to do careful counting** instead of constructing explicit solutions.
3. **Requires a solid understanding of number theory** (`lcm`, `gcd`) in a programming context.
4. **Demonstrates your skill with 64‑bit arithmetic** – most interviewers will ask you to explain why 32‑bit is unsafe.
5. **Highlights how to design a safe upper bound** for the binary search range – a small detail that can trip many candidates.

> *If you can explain the above algorithm in under 5 minutes, you’ll impress almost every hiring manager.*  

---

## 8. Key Take‑aways for Your Next Interview

| Take‑away | Why it matters |
|-----------|----------------|
| **Explain the “possible(M)” check in words** (not just code). | Shows deep understanding, not just memorisation. |
| **Show the upper bound intuition** (why `2*(c1+c2)*max(d1,d2)` works). | Avoids an interview question about “why does the binary search terminate?”. |
| **Discuss time‑complexity** – log‑scale even for `10¹⁴`. | Demonstrates you can reason about asymptotics under tight constraints. |
| **Mention potential pitfalls**: overflow, 1‑based indexing, handling `other` numbers correctly. | Many candidates drop the “other” pool; you’ll get full marks. |

---

## 9. Conclusion

*The problem is deceptively simple once you realise that it is a counting problem hidden behind a “choose the smallest maximum” question.*  
Binary search on the maximum coupled with an `O(1)` feasibility test gives a clean, fast, and mathematically rigorous solution that scales to the largest inputs the problem allows.

Happy coding—and good luck nailing that interview!