---
title: LeetCode 466. Count The Repetitions - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Solution Code

Below are three complete, self‑contained solutions that compile on the latest JDK, Python 3.11 and GCC 14.

---

### 1.1  Java (LeetCode 466)

```java
import java.util.HashMap;
import java.util.Map;

public class Solution {
    public int getMaxRepetitions(String s1, int n1, String s2, int n2) {
        if (n1 == 0) return 0;
        // key : index in s2, value : (block index, count of s2 seen)
        Map<Integer, int[]> record = new HashMap<>();

        int index = 0;          // position inside s2
        int count = 0;          // how many s2 we completed
        int block = 0;          // how many s1 blocks processed

        while (block < n1) {
            // process one block of s1
            for (int i = 0; i < s1.length(); i++) {
                if (s1.charAt(i) == s2.charAt(index)) {
                    index++;
                    if (index == s2.length()) {  // one more s2 finished
                        index = 0;
                        count++;
                    }
                }
            }

            block++;

            // cycle detection
            Integer key = index;
            if (record.containsKey(key)) {
                int[] prev = record.get(key);
                int prevBlock = prev[0];
                int prevCount = prev[1];

                // numbers before cycle
                int prefixBlocks = prevBlock;
                int prefixCount  = prevCount;

                // numbers inside cycle
                int cycleBlocks  = block - prevBlock;
                int cycleCount   = count - prevCount;

                // remaining blocks after full cycles
                int remainingBlocks = n1 - block;
                int fullCycles = remainingBlocks / cycleBlocks;
                int tailBlocks  = remainingBlocks % cycleBlocks;

                int totalCount = prefixCount
                               + fullCycles * cycleCount
                               + getTailCount(s1, s2, index, tailBlocks, count, prevCount);

                return totalCount / n2;
            }

            // remember current state
            record.put(index, new int[]{block, count});
        }

        // no cycle detected
        return count / n2;
    }

    /** counts how many more s2 we can get in the tail part. */
    private int getTailCount(String s1, String s2, int startIndex,
                             int tailBlocks, int curCount, int prevCount) {
        int index = startIndex;
        int count = curCount;
        for (int i = 0; i < tailBlocks; i++) {
            for (int j = 0; j < s1.length(); j++) {
                if (s1.charAt(j) == s2.charAt(index)) {
                    index++;
                    if (index == s2.length()) {
                        index = 0;
                        count++;
                    }
                }
            }
        }
        return count - prevCount;
    }
}
```

*Time*: **O(|s1| + |s2|)**  
*Space*: **O(|s2|)** – the map can contain at most `|s2|` different indices.

---

### 1.2  Python (LeetCode 466)

```python
from typing import Dict, Tuple

class Solution:
    def getMaxRepetitions(self, s1: str, n1: int, s2: str, n2: int) -> int:
        if n1 == 0:
            return 0

        # key : position in s2, value : (block_index, count_of_s2)
        record: Dict[int, Tuple[int, int]] = {}

        idx_s2 = 0     # current position in s2
        count = 0      # number of s2 finished
        block = 0      # number of s1 blocks processed

        while block < n1:
            for ch in s1:
                if ch == s2[idx_s2]:
                    idx_s2 += 1
                    if idx_s2 == len(s2):
                        idx_s2 = 0
                        count += 1
            block += 1

            # cycle detection
            if idx_s2 in record:
                prev_block, prev_count = record[idx_s2]

                # prefix
                prefix_blocks = prev_block
                prefix_count  = prev_count

                # cycle
                cycle_blocks = block - prev_block
                cycle_count  = count - prev_count

                remaining_blocks = n1 - block
                full_cycles = remaining_blocks // cycle_blocks
                tail_blocks  = remaining_blocks % cycle_blocks

                total = (
                    prefix_count
                    + full_cycles * cycle_count
                    + self._tail_count(
                        s1, s2, idx_s2, tail_blocks, count, prev_count
                    )
                )
                return total // n2

            record[idx_s2] = (block, count)

        # no cycle found
        return count // n2

    @staticmethod
    def _tail_count(s1: str, s2: str, start: int,
                    tail_blocks: int, cur_count: int,
                    prev_count: int) -> int:
        idx = start
        cnt = cur_count
        for _ in range(tail_blocks):
            for ch in s1:
                if ch == s2[idx]:
                    idx += 1
                    if idx == len(s2):
                        idx = 0
                        cnt += 1
        return cnt - prev_count
```

*Time*: **O(|s1| + |s2|)**  
*Space*: **O(|s2|)**

---

### 1.3  C++ (LeetCode 466)

```cpp
#include <string>
#include <unordered_map>
#include <vector>

class Solution {
public:
    int getMaxRepetitions(std::string s1, int n1,
                          std::string s2, int n2) {
        if (n1 == 0) return 0;

        // map: index in s2 -> pair(blockIndex, countS2)
        std::unordered_map<int, std::pair<int, int>> record;

        int idxS2 = 0;     // position in s2
        int count = 0;     // number of s2 finished
        int block = 0;     // number of s1 blocks processed

        while (block < n1) {
            for (char ch : s1) {
                if (ch == s2[idxS2]) {
                    ++idxS2;
                    if (idxS2 == (int)s2.size()) {
                        idxS2 = 0;
                        ++count;
                    }
                }
            }
            ++block;

            // cycle detection
            auto it = record.find(idxS2);
            if (it != record.end()) {
                int prevBlock = it->second.first;
                int prevCount = it->second.second;

                // prefix part
                int prefixCount = prevCount;

                // cycle part
                int cycleBlock  = block - prevBlock;
                int cycleCount  = count - prevCount;

                int remaining = n1 - block;
                int fullCycles = remaining / cycleBlock;
                int tailBlocks = remaining % cycleBlock;

                int total =
                    prefixCount
                    + fullCycles * cycleCount
                    + tailCount(s1, s2, idxS2, tailBlocks, count, prevCount);

                return total / n2;
            }
            record[idxS2] = {block, count};
        }

        // no cycle detected
        return count / n2;
    }

private:
    /* how many extra s2 can be collected in the tail part */
    int tailCount(const std::string& s1, const std::string& s2,
                  int startIdx, int tailBlocks,
                  int curCount, int prevCount) const {
        int idx = startIdx;
        int cnt = curCount;
        for (int i = 0; i < tailBlocks; ++i) {
            for (char ch : s1) {
                if (ch == s2[idx]) {
                    ++idx;
                    if (idx == (int)s2.size()) {
                        idx = 0;
                        ++cnt;
                    }
                }
            }
        }
        return cnt - prevCount;
    }
};
```

*Time*: **O(|s1| + |s2|)**  
*Space*: **O(|s2|)**

> **All three implementations use the same idea** – traverse `s1` `n1` times, keep the current index inside `s2`, count how many complete `s2` strings we can extract, and look for a cycle to skip the repeated work.  
> The cycle detection guarantees that the algorithm never exceeds a few hundred iterations (because there are only `|s2|` distinct indices).

---

## 2.  Blog Article – “Cracking LeetCode 466: Count The Repetitions”

### 🗞️ Meta Description  
**LeetCode 466 – Count The Repetitions** – Master the hardest string subsequence problem with a cycle‑based algorithm. Read the Java, Python, and C++ solutions, complexity analysis, interview prep tips, and land your next software engineering job!

---

### 🔎 1. Problem Overview

| Item | Details |
|------|---------|
| **LeetCode ID** | 466 |
| **Name** | Count The Repetitions |
| **Difficulty** | Hard |
| **Tags** | String, Subsequence, Two‑Pointer, Hash, Algorithm, Interview |

**Statement**  
Given two strings `s1` and `s2`, repeat `s1` exactly `n1` times (forming `S1 = s1.repeat(n1)`) and repeat `s2` exactly `n2` times (forming `S2 = s2.repeat(n2)`).  
`S2` is a **subsequence** of `S1` if we can delete characters from `S1` to obtain `S2`.  
Return the maximum integer `k` such that `S2` repeated `k` times is a subsequence of `S1`.  
In formal terms: `k = ⌊ maxMatches / n2 ⌋`.

---

### 2.  Why This Problem Is Interesting

| ✔️  Good | ⚠️  Bad | 🤡  Ugly |
|---------|--------|----------|
| **Good** | • Requires a deep understanding of subsequence logic. | • The naive `O(n1·|s1|·|s2|)` solution is too slow. | • Mis‑reading “repeat” as “concatenate” leads to incorrect results. |
| **Bad** | • The constraints (`n1, n2 ≤ 10000`) force you to think in terms of *cycles* rather than brute force. | • It’s easy to get lost in pointer arithmetic when `s1` and `s2` are of different lengths. | • Failing to memoize states leads to `TLE` on large inputs. |
| **Ugly** | • The official editorial is concise but not beginner‑friendly. | • Some languages (e.g., older C++ compilers) don’t support the `unordered_map` of pairs elegantly. | • People sometimes over‑engineer by using DP tables or `std::vector<int>` of size `n1`, which consumes unnecessary memory. |

---

### 3.  Algorithmic Insight

1. **Single‑pass traversal**  
   Scan `s1` block by block.  
   Keep an index `idxS2` into `s2`.  
   When a character of `s1` matches the current character of `s2`, advance `idxS2`.  
   Whenever `idxS2` reaches the end of `s2`, we’ve extracted one full `s2` and reset `idxS2` to `0`.  
   Increment a `count` that tracks how many `s2`s we have extracted.

2. **Cycle detection**  
   The only thing that matters after processing each block of `s1` is the current `idxS2`.  
   There are only `|s2|` possible values for `idxS2`.  
   If the same `idxS2` appears again, we’ve entered a *cycle*: the pattern of future completions repeats.  
   Record the block index and the `count` when each unique `idxS2` first appears.  
   Once a cycle is found, decompose the remaining work into:

   * **Prefix** – part before the cycle starts.  
   * **Cycle** – the repeatable segment.  
   * **Tail** – the leftover blocks that don’t form a full cycle.

3. **Compute the total number of `s2`**  
   ```
   total = prefixCount
          + fullCycles * cycleCount
          + tailCount
   ```

   `tailCount` is computed by simulating the remaining `tailBlocks` (at most one cycle’s length).

4. **Answer** – divide by `n2` (integer division).

The algorithm runs in linear time with respect to the lengths of `s1` and `s2` plus a small constant for the cycle search. Memory consumption is bounded by `|s2|`.

---

### 4.  Code Snippets

| Language | Key File | Complexity |
|----------|----------|------------|
| **Java** | `Solution.java` | **Time** O(|s1|+|s2|) **Space** O(|s2|) |
| **Python** | `solution.py` | **Time** O(|s1|+|s2|) **Space** O(|s2|) |
| **C++** | `Solution.cpp` | **Time** O(|s1|+|s2|) **Space** O(|s2|) |

*Full code blocks are shown above.*

---

### 5.  Interview Tips

| Topic | Why It Matters | Practical Hint |
|-------|----------------|----------------|
| **Two‑pointer technique** | Keeps track of positions in `s2` while scanning `s1`. | Think of `s1` as the “outer” loop and `s2` as the “inner” pointer. |
| **Cycle detection** | Allows us to skip huge repetitions (`n1` up to 10 000). | Store the *state* (index in `s2`) in a map; once seen again you know you’re looping. |
| **Complexity analysis** | Interviewers love clean Big‑O discussion. | Highlight the linear time and constant space (O(|s2|)). |
| **Edge cases** | Empty strings, `n1==0`, `s2` longer than `s1`. | Test these before coding. |

> **Pro tip** – When explaining your solution, mention that this is a classic *“find the period”* problem that appears in many string‑subsequence interview questions. It demonstrates a deep grasp of how to optimize beyond brute force.

---

### 6.  Why This Solution Gets You the Job

1. **Clear, idiomatic code** – No unnecessary arrays, every variable has a single purpose.  
2. **Scalable algorithm** – Works comfortably for the worst‑case limits (`n1, n2 ≤ 10 000`).  
3. **Prepared for follow‑ups** – If the interviewer asks “What if `s1` or `s2` is extremely long?”, you can confidently explain the cycle‑based approach.  
4. **Versatile** – Demonstrates your ability to translate the same logic across Java, Python and C++ – a red‑flag for companies that use multiple tech stacks.

---

## 2.  SEO‑Optimized Blog Post

> **Title**: *Master LeetCode 466 – Count The Repetitions: Java, Python, C++ & Interview Prep*  

> **Meta Description**: Learn the optimal solution for LeetCode 466 – Count The Repetitions. Dive into the hard string subsequence problem, explore cycle detection, and see the Java, Python, and C++ implementations that help you ace your next tech interview.

```markdown
# Master LeetCode 466 – Count The Repetitions (Hard)

**LeetCode 466** is a favorite among senior software engineers and data‑structure enthusiasts.  
This post walks you through a fast, cycle‑based solution in **Java**, **Python**, and **C++**.  
We’ll dissect why the problem is challenging, highlight key interview concepts, and give you the edge to land your dream role.

---

## 🚀 Problem Snapshot

> **Goal**:  
> - Repeat `s1` `n1` times → `S1`.  
> - Repeat `s2` `n2` times → `S2`.  
> - Find maximum `k` such that `S2` repeated `k` times is a subsequence of `S1`.  

### Why It’s Hard

- It’s not just about concatenation; you must **delete** characters from `S1` to match `S2`.  
- Brute‑force `O(n1·|s1|·|s2|)` is unacceptable for `n1, n2 ≤ 10,000`.  
- Misunderstanding “repeat” versus “concatenate” leads to bugs.

---

## 📈 Algorithmic Breakthrough

1. **Linear scan of `s1`** – Two‑pointer technique.  
2. **Track completions of `s2`** – Use an index `idxS2` and a counter `count`.  
3. **Detect cycles** – Only `|s2|` possible `idxS2` states; use a map.  
4. **Skip repetitions** – Compute prefix, cycle, and tail to get `totalMatches`.  
5. **Result** – Integer division by `n2`.

The algorithm runs in **O(|s1| + |s2|)** time and **O(|s2|)** space.  

---

## ⚙️ Code in Three Languages

| Language | Complexity |
|----------|------------|
| **Java** | **Time** O(|s1|+|s2|), **Space** O(|s2|) |
| **Python** | **Time** O(|s1|+|s2|), **Space** O(|s2|) |
| **C++** | **Time** O(|s1|+|s2|), **Space** O(|s2|) |

> *Full source code is provided in the article above.*

---

## 🧠 Interview Prep Checklist

- Two‑pointer strategy for subsequence matching.  
- State‑based cycle detection to avoid TLE.  
- Edge‑case handling (`n1==0`, empty strings).  
- Complexity discussion: linear time, constant extra space.

---

## 🎯 Get Hired

Show recruiters that you can solve a hard LeetCode problem with clean, efficient code across multiple languages.  
This demonstrates:

1. **Algorithmic depth** – cycle detection is a proven technique.  
2. **Language versatility** – ability to write idiomatic Java, Python, and C++.  
3. **Problem‑solving mindset** – you focus on optimization, not brute force.

---

**Happy coding!** 🚀
```

---

### 📌  Key SEO Keywords

- LeetCode 466
- Count The Repetitions
- Hard string subsequence problem
- Java string algorithm
- Python two‑pointer technique
- C++ unordered_map cycle detection
- Interview prep for string problems
- Data structures and algorithms
- Software engineering interview solutions

---

### 3.  Final Thought

LeetCode 466 teaches you how to transform a seemingly intractable string‑matching challenge into an elegant, efficient solution.  
Whether you code in **Java**, **Python**, or **C++**, the core idea stays the same: scan once, remember your state, find the cycle, and skip the rest.  

Use the code snippets and interview insights above to **confidently explain** the problem in your next interview and to **show your mastery** of advanced string manipulation techniques. Good luck! 🚀

--- 

*End of blog post.*  

--- 

This comprehensive guide, combining production‑ready code and interview‑ready explanations, should empower you to dominate LeetCode 466 and impress recruiters at any top‑tech company. Happy solving!