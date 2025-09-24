---
title: LeetCode 2167. Minimum Time to Remove All Cars Containing Illegal Goods - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚂 LeetCode 2167 – Minimum Time to Remove All Cars Containing Illegal Goods  
*Hard | O(n) time, O(1) space | Java | Python | C++*

---

### 📌 Problem Recap

You are given a binary string `s` where  
- `s[i] = '0'` – the car is *clean*  
- `s[i] = '1'` – the car contains *illegal goods*

You may perform any of the following operations any number of times:

| Operation | Time Cost |
|-----------|-----------|
| Remove from **left** end (`s[0]`) | 1 |
| Remove from **right** end (`s[n-1]`) | 1 |
| Remove **anywhere** in the string | 2 |

Return the *minimum* total time needed to delete **every** `1` from the string.  
(An empty string counts as “no illegal goods”.)

> **Examples**  
> `s = "1100101"` → answer = `5`  
> `s = "0010"` → answer = `2`

---

### 💡 Core Insight

Instead of thinking in terms of “where to delete first”, think of **splitting the string** at a pivot `p`:

```
[ 0 … p-1 | p … n-1 ]
```

* For the left part `0 … p-1` we can:
  * either delete everything from the left end (`cost = p`)
  * or delete each `1` individually (`cost = 2 * (#ones in that part)`)

* For the right part `p … n-1` we can do the symmetric operation.

If we know the *best* cost for the left side up to every index `i`, we can scan once and evaluate the best total cost for every pivot in O(n).

---

### 🔁 One‑Pass O(1)‑Space Algorithm

1. **Initialize**  
   - `n = s.length`  
   - `leftCost = 0` – best cost to clear everything up to the current index (using only left‑end or middle deletes)  
   - `answer = n` – worst case: delete everything from the left

2. **Scan left → right**  
   For each index `i` (0‑based):
   - `leftCost = min(leftCost + (s[i] == '1' ? 2 : 0), i + 1)`  
     *`i+1`* is the cost if we decide to delete the entire prefix `[0 … i]` from the left.  
   - `answer = min(answer, leftCost + (n - 1 - i))`  
     *`n-1-i`* is the cost of deleting the suffix `[i+1 … n-1]` from the right.

3. **Return** `answer`.

Why it works?  
- `leftCost` always represents the *optimal* cost to remove all `1`s in the prefix `[0 … i]` using the allowed operations (either by trimming the whole prefix or by deleting each `1` individually).  
- Adding `n-1-i` accounts for the best way to remove the remaining suffix (trimming it from the right).  
- By updating `answer` at each step we consider every possible pivot position.

---

### ⏱️ Complexity

| Metric | Value |
|--------|-------|
| Time   | **O(n)** |
| Space  | **O(1)** |

`n` ≤ 200 000, so this linear solution easily passes all tests.

---

## 📦 Code Implementations

Below are three fully‑working, self‑contained solutions – one for each requested language.  
All use the same algorithm, just syntax differences.

---

### 🟦 Java

```java
class Solution {
    public int minimumTime(String s) {
        int n = s.length();
        int leftCost = 0;
        int answer = n;                // worst case: delete all from the left

        for (int i = 0; i < n; i++) {
            // cost if we keep deleting the current '1' (or nothing if it's '0')
            int deleteMiddle = leftCost + (s.charAt(i) == '1' ? 2 : 0);
            // cost if we trim the entire prefix [0..i] from the left
            int trimLeft = i + 1;
            leftCost = Math.min(deleteMiddle, trimLeft);

            // best cost for the suffix after i (trim from the right)
            int rightTrim = n - 1 - i;
            answer = Math.min(answer, leftCost + rightTrim);
        }
        return answer;
    }
}
```

---

### 🐍 Python

```python
class Solution:
    def minimumTime(self, s: str) -> int:
        n = len(s)
        left_cost = 0
        answer = n

        for i, ch in enumerate(s):
            delete_middle = left_cost + (2 if ch == '1' else 0)
            trim_left = i + 1
            left_cost = min(delete_middle, trim_left)

            right_trim = n - 1 - i
            answer = min(answer, left_cost + right_trim)

        return answer
```

---

### 🔨 C++

```cpp
class Solution {
public:
    int minimumTime(string s) {
        int n = s.size();
        int leftCost = 0;
        int answer = n;                 // worst case

        for (int i = 0; i < n; ++i) {
            int deleteMiddle = leftCost + (s[i] == '1' ? 2 : 0);
            int trimLeft = i + 1;
            leftCost = min(deleteMiddle, trimLeft);

            int rightTrim = n - 1 - i;
            answer = min(answer, leftCost + rightTrim);
        }
        return answer;
    }
};
```

All three compiles under the latest LeetCode compiler settings (Java 17, Python 3.10, C++17).

---

## 📄 Blog Article: “The Good, The Bad, and The Ugly of LeetCode 2167”

> **SEO Keywords:**  
> *LeetCode 2167, Minimum Time to Remove All Cars Containing Illegal Goods, Java solution, Python solution, C++ solution, algorithm, O(n) time, O(1) space, coding interview, dynamic programming, interview prep*

---

### 1️⃣ The Good – What Makes This Problem a Masterclass

- **Simplicity of the Statement**:  
  A binary string, three operations, find the minimum cost. The rules are easy to grasp.

- **Elegant Linear Solution**:  
  The optimal strategy is a single pass – no nested loops, no extra data structures.  
  The math is surprisingly neat: `min(leftCost + rightTrim, i+1, leftCost+2)`.

- **O(1) Space**:  
  Many “hard” LeetCode problems require DP tables. Here, you only need two integers.

- **Real‑World Analogy**:  
  Imagine cleaning up a train. This model mirrors real decision‑making: cut from ends or grab in the middle. Interviewers love problems with such intuitive storytelling.

---

### 2️⃣ The Bad – Common Pitfalls and Misconceptions

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| **Assuming you must delete all zeros first** | Misreading the goal as “delete all cars” | The goal is “delete all *ones*”; zeros can be ignored. |
| **Using a naïve DP array of size n** | Over‑engineering | Keep a running cost (`leftCost`) and avoid an array. |
| **Not handling `n-1-i` correctly** | Forgetting that the suffix can be trimmed from the right | Add `n - 1 - i` to the prefix cost; this is the right‑trim cost. |
| **Edge case `n = 1`** | Off‑by‑one errors | Initialize `answer = n` and compute `rightTrim = n - 1 - i`. Works for single character. |
| **Using `i` instead of `i+1` for left‑trim cost** | Zero‑based vs one‑based confusion | Remember that a string of length `i+1` requires `i+1` left deletions. |

### 3️⃣ The Ugly – Edge Cases That Trip Up Even Smart Coders

- **All `0`s**:  
  Your algorithm still runs, but you must make sure not to double‑count the “delete middle” cost (it should stay `0`). The min‑operations handle this gracefully.

- **All `1`s**:  
  The algorithm will decide between trimming the whole string or deleting each `1` individually. Both yield the same cost `n` or `2n`.  
  The min comparison ensures we pick the cheaper one.

- **String of Length 200 000**:  
  Using an array of length `n` (like many DP solutions) would consume memory and slow down due to cache misses. The O(1) solution is the only viable path.

---

### 3️⃣ 🧠 Takeaway – How to Talk About This Problem in an Interview

> **Start with the “pivot” story**: “I’ll choose a place to split the train.”  
> **Show the math quickly**: “Up to index `i`, the cost is `min(prefixTrim, middleDeletes)`. Adding the suffix cost gives the total.”  
> **Mention complexity**: “One pass, constant memory – perfect for a coding interview.”

If you can explain the above in 2–3 minutes, the interviewer will be impressed.

---

### 🎯 How to Use This Solution in Your Interview Prep

1. **Practice on LeetCode** – copy the code into the editor, run test cases, tweak the input.  
2. **Teach the Idea** – explaining the algorithm to a friend or in a mock interview will cement your understanding.  
3. **Explore Variants** – change the cost of the middle operation (e.g., 3 instead of 2) and see how the formula adapts.  
4. **Add Discussion Points** – in a future interview, you can bring up the “good, bad, ugly” points as a demonstration of deep problem understanding.

---

### 📌 Final Thought

LeetCode 2167 proves that *hard* doesn’t always mean *complex*.  
A clear problem statement + a clever observation can yield a fast, memory‑efficient solution that still feels like a real‑world decision‑making process. Master it, and you’ll have a shining example to showcase at your next technical interview.

Happy coding! 🚀

---