---
title: LeetCode 2712. Minimum Cost to Make All Characters Equal - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 2712 – Minimum Cost to Make All Characters Equal  
**Java | Python | C++** |  *Greedy, O(n) solution*  

---

### 📌 TL;DR  
- **Goal** – Flip prefixes or suffixes of a binary string at minimum cost so that all characters become the same.  
- **Key insight** – A flip is only necessary when a character changes from the previous one.  
- **Cost decision** – For every change choose the cheaper of `prefixCost = i` or `suffixCost = n‑i`.  
- **Result** – Single pass, O(n) time, O(1) space.

---

## 1️⃣ Problem Recap

| Item | Description |
|------|-------------|
| **Input** | Binary string `s` (length `n ≤ 10⁵`) |
| **Operations** | 1. **Prefix flip** – choose index `i` and invert `s[0…i]`. Cost `i+1`. <br>2. **Suffix flip** – choose index `i` and invert `s[i…n‑1]`. Cost `n-i`. |
| **Output** | Minimum total cost to make all characters of `s` equal. |

---

## 2️⃣ Intuition – When Do We Need to Flip?

If the string is already all `0`s or all `1`s we need nothing.  
Otherwise, look at **adjacent pairs**:

```
s[i-1]   s[i]
```

*If `s[i] != s[i-1]` the string changes from one block to another.*  
Only at these boundaries a flip is required.  
Flipping anything else would undo a previous decision or be more expensive.

---

## 3️⃣ Greedy Choice

At each change point `i` (1‑based), we have two possibilities:

| Choice | What flips? | Cost |
|--------|-------------|------|
| **Prefix flip** | `s[0…i-1]` | `i` |
| **Suffix flip** | `s[i…n-1]` | `n-i` |

Because flips are *independent* – after handling the current boundary the rest of the string stays consistent – we can pick the cheaper of the two.  
No future decision will affect this local choice.

---

## 4️⃣ Algorithm

```
cost = 0
for i from 1 to n-1:
    if s[i] != s[i-1]:
        cost += min(i, n-i)
return cost
```

*Indexing in the algorithm is 0‑based.*

---

## 5️⃣ Correctness Proof (Sketch)

1. **Invariant** – After processing positions `0…i`, the substring `s[0…i]` can be made uniform with the computed cost.  
2. **Base** – For `i=0`, no cost is needed.  
3. **Step** –  
   * If `s[i] == s[i-1]` no boundary exists, invariant holds trivially.  
   * If `s[i] != s[i-1]` we must perform a flip that covers this boundary.  
     Choosing the cheaper operation (prefix or suffix) minimizes cost locally.  
     Because subsequent operations cannot reduce the cost paid for this boundary, the greedy choice is optimal.  
4. **Termination** – After the last index, the whole string is uniform, and the accumulated `cost` is minimal.

---

## 6️⃣ Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| **Time** | `O(n)` | `O(n)` | `O(n)` |
| **Space** | `O(1)` | `O(1)` | `O(1)` |

All three implementations run in a single pass with constant extra memory.

---

## 7️⃣ Reference Implementations

### 📖 Java

```java
public class Solution {
    public long minimumCost(String s) {
        long cost = 0;
        int n = s.length();
        for (int i = 1; i < n; ++i) {
            if (s.charAt(i) != s.charAt(i - 1)) {
                cost += Math.min(i, n - i);
            }
        }
        return cost;
    }
}
```

---

### 📖 Python

```python
class Solution:
    def minimumCost(self, s: str) -> int:
        n = len(s)
        return sum(
            min(i, n - i)
            for i in range(1, n)
            if s[i] != s[i - 1]
        )
```

---

### 📖 C++

```cpp
class Solution {
public:
    long long minimumCost(string s) {
        long long cost = 0;
        int n = s.size();
        for (int i = 1; i < n; ++i) {
            if (s[i] != s[i - 1]) {
                cost += min(i, n - i);
            }
        }
        return cost;
    }
};
```

---

## 8️⃣ Edge Cases & Testing

| Test | Input | Expected | Why |
|------|-------|----------|-----|
| All same | `"000000"` | `0` | No flips needed. |
| Alternating | `"010101"` | `9` | As given in the problem statement. |
| Single char | `"1"` | `0` | Already uniform. |
| Long block change | `"1111100000"` | `5` | One boundary at index 5 → `min(5,5)=5`. |

---

## 9️⃣ Good, Bad, and Ugly of This Problem

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Clarity** | Problem statement is concise. | Operations are described in two ways; may cause confusion. | The cost formulas involve off‑by‑one errors if not careful. |
| **Algorithmic Depth** | Greedy + linear scan – easy to reason. | Requires spotting that only boundaries matter. | Over‑engineering (DP, segment trees) is unnecessary and slower. |
| **Testing** | Small examples are enough. | Need to check edge cases: single char, all same, alternating. | A typo in the cost formula (`i+1` vs `i`) can lead to subtle bugs. |
| **Interview Value** | Shows ability to reduce a problem to simple observations. | Might trick candidates into thinking they need complex DP. | Understanding why local optimality leads to global optimality is subtle. |

---

## 🔍 SEO Keywords (for job‑hunt blog posts)

- LeetCode 2712 solution
- Minimum Cost to Make All Characters Equal
- Binary string flip operations
- Greedy algorithm LeetCode
- O(n) string manipulation problems
- Java/Python/C++ interview coding
- Interview preparation for software engineers
- Algorithmic thinking for binary strings
- Optimize cost operations interview

---

## 📄 Meta Information (for the blog platform)

```html
<title>LeetCode 2712 – Minimum Cost to Make All Characters Equal (Java, Python, C++)</title>
<meta name="description" content="Solve LeetCode 2712 in Java, Python, and C++ with a simple O(n) greedy algorithm. Learn why only string boundaries matter, how to pick the cheaper flip, and test edge cases. Perfect for interview prep!">
```

---

## 📝 Final Summary

LeetCode 2712 is a textbook greedy problem:  
1. **Observe** that flips are only needed at character changes.  
2. **Decide** locally with `min(i, n‑i)` at each boundary.  
3. **Accumulate** the costs in a single pass.  

The resulting code is short, fast, and passes all test cases – exactly the kind of clean solution interviewers look for. Good luck on your coding interviews!