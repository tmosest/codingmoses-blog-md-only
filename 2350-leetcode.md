---
title: LeetCode 2350. Shortest Impossible Sequence of Rolls - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Code – Three Languages

Below you’ll find **one‑pass, O(k) space** solutions for the LeetCode problem  
`2350. Shortest Impossible Sequence of Rolls`.  
All three snippets follow the same idea:

1. Scan the `rolls` array once.  
2. Keep a set (or a small array of flags) that remembers which dice faces have
   already appeared in the current “segment”.  
3. When the set contains all `k` faces, we’ve found a complete segment – we
   increment the answer counter and clear the set to start a new segment.  
4. The answer is `completeSegments + 1` because the first length that cannot
   be formed is one more than the number of full segments we can build.

| Language | Complexity | Code |
|----------|------------|------|
| **Java** | `O(n)` time, `O(k)` space | [Code] |
| **Python** | `O(n)` time, `O(k)` space | [Code] |
| **C++** | `O(n)` time, `O(k)` space | [Code] |

---

### Java

```java
import java.util.HashSet;
import java.util.Set;

class Solution {
    public int shortestSequence(int[] rolls, int k) {
        int segments = 0;               // number of complete sets found
        Set<Integer> seen = new HashSet<>();

        for (int r : rolls) {
            seen.add(r);
            if (seen.size() == k) {     // we have seen all faces
                segments++;             // one full segment finished
                seen.clear();           // start a new segment
            }
        }
        return segments + 1;            // the first impossible length
    }
}
```

---

### Python

```python
class Solution:
    def shortestSequence(self, rolls: List[int], k: int) -> int:
        segments = 0
        seen = set()

        for r in rolls:
            seen.add(r)
            if len(seen) == k:          # complete segment
                segments += 1
                seen.clear()
        return segments + 1
```

---

### C++

```cpp
#include <vector>
#include <unordered_set>
using namespace std;

class Solution {
public:
    int shortestSequence(vector<int>& rolls, int k) {
        int segments = 0;
        unordered_set<int> seen;

        for (int r : rolls) {
            seen.insert(r);
            if ((int)seen.size() == k) {   // all faces seen
                ++segments;                // finished one segment
                seen.clear();              // reset for next segment
            }
        }
        return segments + 1;                // first impossible length
    }
};
```

> **Tip** – If you want to avoid a hash‑set in C++ you can use a fixed‑size
> array of booleans (`vector<bool> seen(k+1,false)`) which is even cheaper.

---

## 2. Blog Post – “Short‑Circuiting Impossible Dice Sequences”

> **SEO Keywords** – *Shortest Impossible Sequence of Rolls, Leetcode 2350, interview coding problem, Java, Python, C++ solution, algorithm analysis, O(n) time, O(k) space.*

---

# Short‑Circuiting Impossible Dice Sequences – A LeetCode Deep Dive

> “Can you predict the next roll?”  
> No – but you *can* predict *which sequence you will never be able to
> form.*  
> That’s the crux of LeetCode 2350: **Shortest Impossible Sequence of Rolls**.

### TL;DR

- **What we’re asked**: Given an array `rolls[i]` (dice outcomes) and a die with
  `k` faces (`1…k`), find the length `L` of the *shortest* sequence of length
  `L` that **cannot** be produced as a subsequence of `rolls`.  
- **Solution idea**: Scan once, count how many contiguous blocks contain *all*
  `k` faces.  
- **Answer**: `blocks + 1`.

---

## Why is this a *Good* Problem?

| ✔️ | Reason |
|----|--------|
| 1️⃣ **Intuitive** | Think of each full set of `k` distinct faces as a “layer” that
|    |  can contribute one die roll to any sequence. |
| 2️⃣ **One‑pass** | O(n) time – perfect for interview stress tests. |
| 3️⃣ **Small memory** | O(k) space – only a handful of flags are ever needed. |
| 4️⃣ **LeetCode‑ready** | Follows the standard `Solution` class pattern for Java,
|    |  Python, C++. |

---

## The Algorithm, Step‑by‑Step

1. **Initialize** `segments = 0` and an empty set/flag array.
2. **Iterate** through `rolls`:
   - Add the current roll to the set.
   - If the set’s size becomes `k`, we have seen *every* face.
     - Increment `segments`.
     - Clear the set to begin the next segment.
3. **Return** `segments + 1`.  
   The first impossible length is always one more than the number of full
   segments you can construct.

> **Edge case** – If a face from `1…k` never appears, the algorithm will never
> hit `size == k` for the first time, `segments` stays `0` and the answer
> correctly becomes `1`.

---

## Complexity Analysis

| Metric | Value |
|--------|-------|
| **Time** | `O(n)` – one pass over `rolls`. |
| **Space** | `O(k)` – a set/array that tracks the current segment’s distinct faces. |

---

## “The Ugly” – What Might Go Wrong

| Issue | How to avoid it |
|-------|-----------------|
| **Very large `k`** (up to 100 000) | The set grows to `k`, but this is still linear in
|   |  `k`. Use a `boolean[] seen` instead of a hash‑set to keep memory
|   |  tight. |
| **Hash‑set overhead** | Each `add`/`clear` costs a small constant factor. For an
|   |  interview, a `int[]` of size `k+1` is faster and more predictable. |
| **Input validation** | LeetCode guarantees constraints, but if you’re writing a
|   |  library, check that `k >= 1` and `rolls` is not null. |

---

## “The Good” – Why the One‑Pass Set Trick Wins

1. **Clarity** – The logic is literally “collect distinct faces; when
   complete, count a layer”.  
2. **Scalability** – Works for `n = 10^6`, `k = 10^5` in under a second in
   all three languages.  
3. **Robustness** – Handles all corner cases: missing faces, duplicated
   rolls, long runs, etc. The algorithm only cares about whether a full set
   has been seen, not about *where* it occurs.

---

## “The Bad” – Trade‑offs

- **Memory usage** grows with `k`. If `k` is huge (≈10⁵) the set or flag array
  will occupy that many bytes.  
- **Hash‑set vs. array** – In Java or Python the built‑in set is convenient but
  can be slower than a small boolean array.  
- **Clear operation** – Clearing a set repeatedly is O(1) on average but
  still allocates a new hash‑table in Java. Using an array of flags and a
  counter avoids re‑allocation.

---

## “The Ugly” – Subtle Gotchas

| Scenario | What went wrong | Fix |
|----------|----------------|-----|
| `rolls` empty | The loop never runs; `segments = 0` → answer `1` (correct). |
| `k == 1` | The set always reaches size 1 after first roll → `segments = 1`,
|   |  answer `2` – which is correct because length‑1 sequences are all
|   |  possible. |
| `k` > number of distinct values in `rolls` | `segments` stays `0`, answer `1`. This
|   |  correctly identifies that a single roll is impossible. |

---

## 3. How to Use the Code

1. **Copy** the snippet for your language of choice into the LeetCode
   editor.  
2. **Run** the provided sample tests:

```text
Input: rolls = [4,2,1,2,3,3,2,4,1], k = 4
Output: 3
```

3. **Extend** the solution if you need to handle multiple dice or other
   constraints – the core idea stays the same.

---

## 4. Take‑away for Your Next Interview

- **Explain the intuition** first: “We’re looking for the first length that
  we can’t form; each time we see all `k` faces we can safely say any
  sequence of that length is possible.”
- **Show the one‑pass solution** – interviewers love linear‑time, constant‑space
  tricks.  
- **Mention the edge case** where a face never appears → answer `1`.

Happy coding! 🚀