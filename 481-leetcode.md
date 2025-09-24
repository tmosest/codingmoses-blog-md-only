---
title: LeetCode 481. Magical String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🌟 Leetcode 481 – Magical String  
### Java | Python | C++ – Full Code + In‑Depth Blog  

> **SEO Keywords**: *Leetcode 481, Magical String, Java solution, Python solution, C++ solution, interview coding problem, algorithm interview, job interview tips, data structure, time complexity, space complexity.*

---

### Table of Contents  

1. [Problem Overview](#problem-overview)  
2. [Intuitive Understanding](#intuitive-understanding)  
3. [Good: Elegant O(n) Approach](#good-approach)  
4. [Bad: Brute‑Force Pitfall](#bad-approach)  
5. [Ugly: Common Gotchas](#ugly-approach)  
6. [Java Implementation](#java-implementation)  
7. [Python Implementation](#python-implementation)  
8. [C++ Implementation](#cpp-implementation)  
9. [Complexity Analysis](#complexity-analysis)  
10. [Interview Tips & How This Helps Your Resume](#interview-tips)  
11. [Wrap‑Up](#wrap-up)

---

## 1. Problem Overview <a name="problem-overview"></a>

> **Leetcode 481 – Magical String**  
> **Difficulty:** Medium  
> **Question:**  
> A *magical string* `s` consists only of the characters `'1'` and `'2'`.  
> When you look at the lengths of consecutive groups in `s`, you obtain `s` itself.  
>  
> Example:  
> `s = 1221121221221121122…`  
> Groups: `1 | 22 | 11 | 2 | 1 | 22 | 1 | 22 | 11 | 2 | 11 | 22 …`  
> Lengths: `1 2 2 1 1 2 1 2 2 1 2 2 …` → this is exactly `s`.  
>  
> **Task:** Given `n` (1 ≤ n ≤ 100 000), return how many `'1'`s appear in the first `n` characters of `s`.  

> **Input:** `int n`  
> **Output:** `int` – count of `'1'`s in `s[0…n‑1]`

---

## 2. Intuitive Understanding <a name="intuitive-understanding"></a>

The magical string is built **self‑referentially**:

1. Start with `s = "122"`.  
2. The *next* group length is given by the next element in the string itself.  
3. We toggle the value (`'1'` ↔ `'2'`) for each group.  

So we can generate the string on the fly while counting `'1'`s:

```
current_value = 1          // first group is '1'
index_to_read   = 0        // where we read the next length
while (generated_length < n)
    group_len = s[index_to_read]   // how many of current_value
    add group_len to s
    if current_value == 1:   count += min(group_len, n - generated_length)
    generated_length += group_len
    current_value ^= 3       // flip 1<->2
    index_to_read++
```

This algorithm is **O(n)** in time and **O(n)** in space (only the first `n` characters are stored).

---

## 3. Good: Elegant O(n) Approach <a name="good-approach"></a>

- **Linear time:** Each position of the string is processed once.  
- **Space‑saving:** Only the prefix up to `n` is stored.  
- **No string concatenation loops:** Avoids quadratic cost.  

Key idea: **Use the string itself as a source of run‑lengths.**  
We keep a pointer `idx` that walks through the string; each `s[idx]` tells us how many times the current digit (`1` or `2`) should appear next.

---

## 4. Bad: Brute‑Force Pitfall <a name="bad-approach"></a>

A naive approach would:

1. Generate the string character by character.  
2. After each addition, scan the entire string to recompute the next group length.  

**Why it’s bad:**

- **O(n²)** time – scanning repeatedly.  
- **High constant factor** – unnecessary overhead.  
- **Memory waste** – may keep a whole string that’s far larger than needed.

Most interviewers will instantly flag this as non‑optimal.

---

## 5. Ugly: Common Gotchas <a name="ugly-approach"></a>

| Issue | Symptom | Fix |
|-------|---------|-----|
| Off‑by‑one when counting `'1'`s | `count` overshoots `n` | Use `min(group_len, n - pos)` |
| Flipping value using `^=3` instead of `^=1` | Wrong toggle for `2` | `current ^= 3` works because 1↔2 (`1 ^ 3 = 2`, `2 ^ 3 = 1`) |
| Using `StringBuilder` incorrectly | Huge memory, slow | Use an `int[]` or `ArrayList<Integer>` |
| Not stopping at `n` | Exceeds array bounds | Break when `generatedLength >= n` |

---

## 6. Java Implementation <a name="java-implementation"></a>

```java
import java.util.ArrayList;
import java.util.List;

public class MagicalString {
    public int magicalString(int n) {
        if (n == 0) return 0;
        // we only need the first n characters
        int[] s = new int[Math.max(3, n)];
        s[0] = 1;
        s[1] = 2;
        s[2] = 2;

        int idx = 0;          // index to read next group length
        int curr = 1;         // current value to write
        int written = 3;      // already written length
        int count1 = 1;       // we already have one '1' in the prefix

        while (written < n) {
            int len = s[idx++];          // length of next run
            for (int i = 0; i < len; i++) {
                if (written >= n) break;
                s[written] = curr;
                if (curr == 1) count1++;
                written++;
            }
            curr ^= 3;   // toggle between 1 and 2 (1 ^ 3 = 2, 2 ^ 3 = 1)
        }
        return count1;
    }

    // simple test harness
    public static void main(String[] args) {
        MagicalString ms = new MagicalString();
        System.out.println(ms.magicalString(6)); // 3
        System.out.println(ms.magicalString(1)); // 1
    }
}
```

> **Why this is good**  
> - Uses a fixed‑size array (`int[]`) – no `StringBuilder` overhead.  
> - Only iterates up to `n`.  
> - Uses bitwise toggle for clarity and speed.

---

## 7. Python Implementation <a name="python-implementation"></a>

```python
def magical_string(n: int) -> int:
    if n == 0:
        return 0

    # start with the known prefix
    s = [1, 2, 2]
    idx = 0          # where we read the next run length
    curr = 1         # current digit to write
    count1 = 1       # we already have one '1'
    while len(s) < n:
        run = s[idx]
        idx += 1
        for _ in range(run):
            if len(s) >= n:
                break
            s.append(curr)
            if curr == 1:
                count1 += 1
        curr ^= 3   # toggle 1 <-> 2

    return count1


# Simple tests
if __name__ == "__main__":
    print(magical_string(6))  # 3
    print(magical_string(1))  # 1
```

> Python’s list automatically resizes, but the algorithm still runs in **O(n)** time and **O(n)** space.

---

## 8. C++ Implementation <a name="cpp-implementation"></a>

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int magicalString(int n) {
        if (n == 0) return 0;

        vector<int> s(max(3, n));
        s[0] = 1; s[1] = 2; s[2] = 2;

        int idx = 0;          // index of next run length
        int curr = 1;         // current digit to write
        int written = 3;      // how many characters already in s
        int count1 = 1;       // we already have one '1'

        while (written < n) {
            int len = s[idx++];
            for (int i = 0; i < len; ++i) {
                if (written >= n) break;
                s[written++] = curr;
                if (curr == 1) ++count1;
            }
            curr ^= 3;   // toggle 1 and 2
        }
        return count1;
    }
};
```

> **Key C++ idioms**  
> - `vector<int>` for dynamic array.  
> - Bitwise toggle `curr ^= 3` (same trick as Java).  

---

## 9. Complexity Analysis <a name="complexity-analysis"></a>

| Approach | Time | Space |
|----------|------|-------|
| Good (ours) | **O(n)** | **O(n)** (only first `n` characters) |
| Brute‑force | **O(n²)** | **O(n)** |
| Ugly (off‑by‑one errors) | O(n) but wrong result | O(n) |

With `n ≤ 100 000`, the O(n) solution runs in milliseconds on modern hardware.

---

## 10. Interview Tips & How This Helps Your Resume <a name="interview-tips"></a>

1. **Start with a clear explanation** – Show you understand the self‑referential property.  
2. **Discuss complexity early** – “I’ll generate only the first n characters, so this is linear.”  
3. **Mention the toggle trick** – Using `curr ^= 3` is a neat bit‑wise trick that shows you’re comfortable with low‑level operations.  
4. **Talk about edge cases** – `n = 1`, `n = 2`, `n` very large.  
5. **Write clean, commented code** – Recruiters value readability.  
6. **If you hit a roadblock, explain why brute‑force fails** – Demonstrates problem‑solving mindset.  

> **Why it looks good on a résumé**  
> - **Algorithmic depth** – Shows you can design linear algorithms.  
> - **Multi‑language proficiency** – Java, Python, C++ code snippets demonstrate versatility.  
> - **Coding interview mastery** – Solving a Leetcode Medium problem is a strong interview signal.  

---

## 11. Wrap‑Up <a name="wrap-up"></a>

The **Magical String** problem is a classic example of a *self‑referential sequence*.  
By treating the string itself as a source of run‑lengths and toggling between `1` and `2`, we arrive at a clean, linear algorithm that runs in under a millisecond for the maximum input size.

- **Java** – `int[]` + bitwise toggle  
- **Python** – dynamic list + same logic  
- **C++** – `vector<int>` + XOR  

Use this solution to impress interviewers, earn a higher rating on Leetcode, and boost confidence in handling elegant algorithmic challenges.  

Happy coding—and may your next interview be a *magical* success! 🚀

--- 

**Meta Description**  
Solve Leetcode 481 “Magical String” in Java, Python, and C++ with an O(n) algorithm. Learn the good, bad, and ugly approaches, plus interview tips to land your next job.