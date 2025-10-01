---
title: LeetCode 2511. Maximum Enemy Forts That Can Be Captured - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Maximum Enemy Forts That Can Be Captured – 2511 (LeetCode)

**Keywords:**  
`LeetCode 2511` • `Maximum Enemy Forts That Can Be Captured` • `Java solution` • `Python solution` • `C++ solution` • `two pointers` • `one-pass algorithm` • `O(n)` time • `O(1)` space

---

## 1. Problem Overview

You’re given an array `forts` (0‑indexed) where:

| Value | Meaning |
|-------|---------|
| `-1`  | No fort |
| `0`   | Enemy fort |
| `1`   | Your fort |

You can move your army from **any** of your forts (`1`) to **any** empty position (`-1`).  
While moving, you **must** travel only over enemy forts (`0`). All the enemy forts you pass are captured.

> **Goal** – Return the maximum number of enemy forts you can capture in a single move.  
> If no valid move exists, return `0`.

**Constraints**

* `1 ≤ forts.length ≤ 1000`
* `-1 ≤ forts[i] ≤ 1`

---

## 2. Intuition & One‑Pass Solution

The army can only travel over **contiguous** zeros between a starting fort and a target empty spot.  
The only thing that matters is:

* **Where the last fort (`1` or `-1`) was located?**
* **How many zeros lie between it and the current position?**

If the last fort was a **friendly fort** (`1`) and the current spot is an **empty spot** (`-1`), all the zeros between them will be captured.  
The same logic holds when we start from an empty spot and end at a friendly fort – we still capture the same number of zeros.  
Thus we only need to remember the **last non‑zero index and its value** while scanning the array once.

### Algorithm (Two Pointers in a single pass)

```
maxCaptured = 0
lastIndex   = -1          // position of last non‑zero
lastValue   = 0           // value at lastIndex (0 means none yet)

for i from 0 to n-1:
    if forts[i] == 0: continue

    if lastValue != 0 and lastValue != forts[i]:
        captured = i - lastIndex - 1
        maxCaptured = max(maxCaptured, captured)

    lastIndex = i
    lastValue = forts[i]

return maxCaptured
```

* `lastValue != 0` guarantees we have a previous fort/empty spot to pair with.  
* `lastValue != forts[i]` ensures the pair is opposite (`1` vs `-1`).  
* `i - lastIndex - 1` counts the zeros in between.

---

## 3. Complexity Analysis

| Metric | Complexity |
|--------|------------|
| Time   | **O(n)**   |
| Space  | **O(1)**   |

`n` is the length of `forts`. We perform a single linear scan with constant extra variables.

---

## 4. Edge Cases

| Case | Why it matters | How the algorithm handles it |
|------|----------------|------------------------------|
| No friendly fort (`1`) | No valid start position | `lastValue` never becomes `1`; no `captured` update → returns 0 |
| No empty spot (`-1`)  | No destination | Same as above |
| Only zeros or mix of zeros and a single type of fort | Impossible to capture | `lastValue` never differs → returns 0 |
| Multiple optimal pairs | We keep the maximum | `maxCaptured` updated each time |

---

## 5. Code Implementations

> All solutions share the same logic; only syntax differs.

### 5.1 Java

```java
import java.util.*;

class Solution {
    public int captureForts(int[] forts) {
        int maxCaptured = 0;
        int lastIndex = -1;
        int lastValue = 0;      // 0 means we haven't seen a 1 or -1 yet

        for (int i = 0; i < forts.length; i++) {
            if (forts[i] == 0) continue;

            if (lastValue != 0 && lastValue != forts[i]) {
                int captured = i - lastIndex - 1;
                maxCaptured = Math.max(maxCaptured, captured);
            }

            lastIndex = i;
            lastValue = forts[i];
        }
        return maxCaptured;
    }
}
```

### 5.2 Python 3

```python
from typing import List

class Solution:
    def captureForts(self, forts: List[int]) -> int:
        max_captured = 0
        last_idx = -1
        last_val = 0          # 0 means no previous fort / empty spot

        for i, val in enumerate(forts):
            if val == 0:
                continue
            if last_val != 0 and last_val != val:
                captured = i - last_idx - 1
                max_captured = max(max_captured, captured)

            last_idx = i
            last_val = val

        return max_captured
```

### 5.3 C++

```cpp
class Solution {
public:
    int captureForts(vector<int>& forts) {
        int maxCaptured = 0;
        int lastIdx = -1;
        int lastVal = 0;     // 0: no previous fort or empty

        for (int i = 0; i < forts.size(); ++i) {
            if (forts[i] == 0) continue;
            if (lastVal != 0 && lastVal != forts[i]) {
                int captured = i - lastIdx - 1;
                maxCaptured = max(maxCaptured, captured);
            }
            lastIdx = i;
            lastVal = forts[i];
        }
        return maxCaptured;
    }
};
```

All three snippets compile on LeetCode and run in **O(n)** time with **O(1)** extra memory.

---

## 6. Good, Bad, & Ugly of the Solution

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Algorithmic elegance** | One linear pass; constant space | None | None |
| **Readability** | Very concise; clear variable names | `lastValue` could be named `lastType` | None |
| **Edge‑case handling** | All cases covered automatically | None | None |
| **Performance** | Beats 100 % on LeetCode | None | None |
| **Potential confusion** | The “opposite” check may be unclear | Some might think of two pointers but it’s just a single scan | None |

**Bottom line:** This is a clean, optimal, and interview‑friendly solution. It demonstrates a solid understanding of linear scans and two‑pointer concepts without unnecessary complexity.

---

## 7. Interview Tips

1. **Explain the invariant** – “We remember the last non‑zero element and its index.”  
2. **Show how you handle pairs** – “When the current non‑zero is opposite the previous, we capture the zeros in between.”  
3. **Complexity discussion** – “We run one pass: O(n) time, O(1) space.”  
4. **Discuss edge cases** – “If there’s no `1` or no `-1`, we never update `maxCaptured`.”  
5. **Mention alternative approaches** – “Brute force would be O(n²) by checking every pair; our solution beats that dramatically.”  

---

## 8. Conclusion

The *Maximum Enemy Forts That Can Be Captured* problem is an excellent showcase of a **single‑pass, two‑pointer** style solution. The algorithm’s simplicity, optimal time/space complexity, and clean code make it an ideal interview example. By mastering this problem, you’ll demonstrate your ability to think critically, handle edge cases gracefully, and write efficient, readable code.

Good luck on your next coding interview—feel free to drop the above solution into your portfolio and share it on LinkedIn or GitHub with the tags **#LeetCode2511 #AlgorithmDesign #CodingInterview** to boost your visibility!