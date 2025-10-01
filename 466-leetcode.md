---
title: LeetCode 466. Count The Repetitions - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 🎯 466 – Count The Repetitions  
### “The Good, The Bad, and The Ugly” – A Job‑Interview‑Ready Guide  
*(LeetCode 466, Hard – perfect for technical interviews, resume demos, and interview prep)*  

---

### 1️⃣ Problem Summary

You’re given:

| Variable | Meaning |
|----------|---------|
| `s1`     | A string (1 – 100 chars) |
| `n1`     | Number of times `s1` is concatenated → `str1 = s1 * n1` |
| `s2`     | A second string (1 – 100 chars) |
| `n2`     | Number of times `s2` is concatenated → `str2 = s2 * n2` |

**Goal** – Find the largest integer `m` such that `str2 * m` can be obtained from `str1` by deleting some (possibly zero) characters.  
In other words, how many times can we “extract” `s2` from the repeated string `s1`?

---

### 2️⃣ Why It Matters

- **LeetCode Hard** → Showcases algorithmic thinking and space‑time trade‑offs.  
- **Interview Goldmine** – Many recruiters ask variations of this question to evaluate mastery of **string manipulation, dynamic programming, and cycle detection**.  
- **Resume Boost** – Solving this in < O(1) memory and ~O(1) time per block demonstrates a deep understanding of **pattern repetition**.

---

### 3️⃣ The “Good” – Intuitive Approach

If we simply simulate every repetition of `s1` and try to match `s2`, we get:

```text
for each block of s1
    for each char in s1
        if it matches current s2 char → advance
```

After finishing one `s1` block we record:

- **`index`** – Position inside `s2` where we left off.  
- **`count`** – How many full `s2` strings have been completed.

Repeat until we reach `n1` blocks.  
Finally, `count / n2` gives the answer.

> **Why “Good”**:  
> • Very easy to understand.  
> • No fancy data structures.  
> • Works for the smallest inputs.  

---

### 4️⃣ The “Bad” – Straightforward but Too Slow

A naive simulation as described above costs **O(n1 · |s1|)** time.  
With `n1` up to **10⁶** and `|s1|` up to **100**, that’s ~10⁸ operations – still doable but *not* optimal, and memory‑heavy if you store all intermediate states.

> **Bad** because:  
> • Time can hit the LeetCode limit on tight test cases.  
> • Requires an array of size `n1` for storing states → O(10⁶) space.

---

### 5️⃣ The “Ugly” – Over‑Engineering the Same Idea

Some solutions attempt to build elaborate **DP tables** or use **suffix automata**.  
While mathematically elegant, they:

- Increase code complexity.  
- Make it harder for interviewers to follow the logic.  
- Often yield the same O(n1) runtime but with a huge constant factor.

---

### 6️⃣ The “Perfect” – Cycle‑Detection in O(|s1| + |s2|)

The key insight:  

> After each `s1` block, we only need to remember the **current index inside `s2`**.  
> If the same index appears again, we’ve entered a cycle – the process will repeat identically from now on.

**Algorithm Steps**

1. **Simulate one `s1` block** – record `index` and `count`.  
2. **Store the pair `(index)` → (block number, count)** in a map.  
3. **When we encounter a previously seen `index`**, we know the cycle:
   * `preCycleCount` – matches before the cycle starts.
   * `cycleCount` – matches per cycle.
   * `cycleLen` – number of `s1` blocks per cycle.
4. **Fast‑forward**:
   * `remainingBlocks = n1 - blockBeforeCycle`.
   * `numCycles = remainingBlocks / cycleLen`.
   * `restBlocks = remainingBlocks % cycleLen`.
5. Compute final `count`:
   ```text
   totalCount = preCycleCount
              + cycleCount * numCycles
              + countAfterRestBlocks
   ```
6. Answer = `totalCount / n2`.

**Complexity**

| Metric | Value |
|--------|-------|
| Time   | O(|s1| + |s2|)  (≤ 200 operations) |
| Space  | O(|s2|)         (map of at most |s2| entries) |

> **Why “Perfect”**:  
> • Constant‑time per `s1` block.  
> • Handles the worst‑case `n1 = 10⁶` instantly.  
> • Minimal memory usage – ideal for interview settings.

---

### 7️⃣ Implementation

Below are clean, idiomatic solutions in **Java, Python, and C++** using the cycle‑detection method.

---

#### 🔧 Java

```java
import java.util.*;

public class Solution {
    public int getMaxRepetitions(String s1, int n1, String s2, int n2) {
        if (n1 == 0) return 0;

        // Map: index in s2 → (blockIdx, count)
        Map<Integer, int[]> record = new HashMap<>();
        int index = 0;          // current position in s2
        int count = 0;          // full s2 matches
        int block = 0;          // how many s1 blocks processed

        while (block < n1) {
            for (int i = 0; i < s1.length(); i++) {
                if (s1.charAt(i) == s2.charAt(index)) {
                    index++;
                    if (index == s2.length()) {
                        index = 0;
                        count++;
                    }
                }
            }

            // if we've seen this index before → cycle found
            if (record.containsKey(index)) {
                int[] prev = record.get(index);
                int preCycleBlocks = prev[0];
                int preCycleCount  = prev[1];

                int cycleBlocks = block + 1 - preCycleBlocks;
                int cycleCount  = count - preCycleCount;

                int remainingBlocks = n1 - (block + 1);
                int cycles = remainingBlocks / cycleBlocks;
                int rest = remainingBlocks % cycleBlocks;

                int finalCount = preCycleCount
                        + cycles * cycleCount;

                // Simulate the remaining blocks
                int idx = index;
                int cnt = 0;
                for (int i = 0; i < rest; i++) {
                    for (int j = 0; j < s1.length(); j++) {
                        if (s1.charAt(j) == s2.charAt(idx)) {
                            idx++;
                            if (idx == s2.length()) {
                                idx = 0;
                                cnt++;
                            }
                        }
                    }
                }
                finalCount += cnt;
                return finalCount / n2;
            }

            // record current state
            record.put(index, new int[]{block + 1, count});
            block++;
        }

        // No cycle – we processed all blocks
        return count / n2;
    }
}
```

> **Key points**  
> • `record` stores only `index` (≤ |s2| entries).  
> • Once a cycle is detected, the loop breaks and we fast‑forward.  

---

#### 🐍 Python (3.8+)

```python
class Solution:
    def getMaxRepetitions(self, s1: str, n1: int, s2: str, n2: int) -> int:
        if n1 == 0:
            return 0

        # index in s2 → (blockIdx, count)
        record = {}
        index = 0
        count = 0
        block = 0

        while block < n1:
            for ch in s1:
                if ch == s2[index]:
                    index += 1
                    if index == len(s2):
                        index = 0
                        count += 1

            # cycle check
            if index in record:
                prev_block, prev_count = record[index]
                pre_blocks = prev_block
                pre_cnt    = prev_count

                cycle_blocks = block + 1 - pre_blocks
                cycle_cnt    = count - pre_cnt

                remaining_blocks = n1 - (block + 1)
                cycles = remaining_blocks // cycle_blocks
                rest   = remaining_blocks % cycle_blocks

                final_count = pre_cnt + cycles * cycle_cnt

                # simulate the rest of the blocks
                idx = index
                cnt = 0
                for _ in range(rest):
                    for ch in s1:
                        if ch == s2[idx]:
                            idx += 1
                            if idx == len(s2):
                                idx = 0
                                cnt += 1
                final_count += cnt
                return final_count // n2

            record[index] = (block + 1, count)
            block += 1

        return count // n2
```

---

#### 📦 C++

```cpp
class Solution {
public:
    int getMaxRepetitions(string s1, int n1, string s2, int n2) {
        if (n1 == 0) return 0;
        unordered_map<int, pair<int, int>> rec;   // index -> {blockIdx, count}
        int idx = 0, cnt = 0, block = 0;

        while (block < n1) {
            for (char c : s1) {
                if (c == s2[idx]) {
                    ++idx;
                    if (idx == (int)s2.size()) {
                        idx = 0;
                        ++cnt;
                    }
                }
            }

            if (rec.count(idx)) {   // cycle detected
                auto [preBlock, preCnt] = rec[idx];
                int cycleBlocks = block + 1 - preBlock;
                int cycleCnt    = cnt - preCnt;

                int remainBlocks = n1 - (block + 1);
                int cycles = remainBlocks / cycleBlocks;
                int rest   = remainBlocks % cycleBlocks;

                int totalCnt = preCnt + cycles * cycleCnt;

                // simulate the rest of the blocks
                int tempIdx = idx;
                int tempCnt = 0;
                for (int r = 0; r < rest; ++r) {
                    for (char c : s1) {
                        if (c == s2[tempIdx]) {
                            ++tempIdx;
                            if (tempIdx == (int)s2.size()) {
                                tempIdx = 0;
                                ++tempCnt;
                            }
                        }
                    }
                }
                totalCnt += tempCnt;
                return totalCnt / n2;
            }

            rec[idx] = {block + 1, cnt};
            ++block;
        }

        return cnt / n2;   // no cycle – we finished all blocks
    }
};
```

---

### 8️⃣ Edge‑Case Checklist

| Test | Expected |
|------|----------|
| `s1 = "abcd", n1 = 1, s2 = "abcd", n2 = 1` → 1 |
| `s1 = "ac",  n1 = 2, s2 = "ca",  n2 = 1` → 0 (no match) |
| `s1 = "a",   n1 = 1000000, s2 = "a", n2 = 1` → 1 000 000 |
| `s1 = "abc", n1 = 4, s2 = "ab", n2 = 2` → 1 |
| **Large**: `s1 = "abc", n1 = 10⁶, s2 = "ab", n2 = 3` → instant O(1) answer |

---

### 9️⃣ Common Interview Traps

1. **Off‑by‑One on the Cycle**  
   – Remember that after finishing the *current* block you’re at `block + 1`.  
   – `record` should store the *next* state, not the current one.

2. **Division vs Modulus**  
   – The final answer is `totalMatches / n2`.  
   – In Java/Python/C++ use integer division; in languages with `/` producing float (Python 2) use `//`.

3. **Large `n1` vs Small `|s1|`**  
   – Even though `n1` can be 10⁶, the cycle will always appear within at most `|s2| + 1` different indices → O(1) memory.

4. **Time Limit on LeetCode**  
   – Avoid nested loops over `n1`.  
   – Use `unordered_map` (C++), `HashMap` (Java), or `dict` (Python) to detect cycles.

---

### 10️⃣ Final Thought

**LeetCode 466 is a showcase problem.**  
Solving it with a cycle‑detection approach demonstrates:

- **Space efficiency** – only O(|s2|) memory.  
- **Time optimality** – O(|s1| + |s2|) regardless of `n1`.  
- **Readability** – clear mapping from problem statement to algorithm.  

These qualities are exactly what recruiters look for when they ask “can you handle large inputs efficiently?” or “how would you speed up a simulation?”  

---

## 🚀 Get Ready for Your Next Interview

1. **Implement the solution in your preferred language** (Java, Python, C++).  
2. **Run the provided unit tests** – they cover corner cases.  
3. **Explain the cycle‑detection logic** clearly – focus on “index in s2” as the key state.  
4. **Mention complexity** and why the approach is optimal.  

> *Tip:* When asked about LeetCode 466, mention the **“cycle detection”** trick.  
> It instantly impresses interviewers and shows you can turn a brute‑force O(n1) into a real O(1) solution.

Happy coding, and may your job interview be *as smooth as a repeated pattern!*

---