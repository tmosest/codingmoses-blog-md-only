---
title: Day 0 LeetCode
description:
date: 2025-09-16
categories: []
author: moses
tags: []
hideToc: true
---

# 1. Two‑Sum – The *Nice, Ugly, and (Un)pleasant* Side of a Classic Interview Problem

> “One of the most common LeetCode problems.”  
> — *“LeetCode‑1001: Two Sum”*  

When you first see the “Two‑Sum” problem, you might think, *“What a simple thing.”* After all, it’s just “find two indices whose values add up to a target.” Yet the problem has a lot more nuance than the name suggests. It’s a perfect playground for talking about algorithmic trade‑offs, code readability, edge‑case gymnastics, and interview expectations.  

In this article we’ll take a close look at:

| Aspect | What it means for Two‑Sum |
|--------|----------------------------|
| **Good** | Why the problem is *useful* for interviews and teaching |
| **Bad** | What makes it *fragile* or *error‑prone* |
| **Ugly** | The “gotchas” that can break even a well‑written solution |

…and how you can write a clean, robust solution that beats the “bad” and avoids the “ugly”.

---

## 1. The Problem (in a nutshell)

```text
Input:
  nums   – array of integers
  target – a single integer

Output:
  A 2‑element array [i, j] such that
  nums[i] + nums[j] == target
  (indices are 0‑based and i != j)
  The input guarantees *exactly one* such pair.
```

Examples:

| `nums` | `target` | Output |
|--------|----------|--------|
| [2,7,11,15] | 9 | [0,1] |
| [3,2,4] | 6 | [1,2] |
| [3,3] | 6 | [0,1] |

Constraints:

- `2 ≤ nums.length ≤ 10⁴`
- `-10⁹ ≤ nums[i], target ≤ 10⁹`
- *Exactly one answer exists.*

---

## 2. Good Things About Two‑Sum

| # | Why it’s a *good* interview problem |
|---|-------------------------------------|
| 1 | **Conceptual clarity** – It isolates a single operation: *search for a complement*. |
| 2 | **Multiple solution paths** – Brute‑force, hash‑table, two‑pointer, sorting, even recursion. |
| 3 | **Scales with “real” data** – The constraints push you toward O(n) solutions, which are common in production. |
| 4 | **Easy to extend** – Add “return all pairs”, “allow duplicates”, or “find pair with smallest index difference” without rewriting the whole logic. |
| 5 | **Language‑agnostic** – It’s a great test of your data‑structure knowledge, not just syntax. |

> *Interviewers love this because it’s short enough to code in ~10 minutes but still forces you to discuss time‑space trade‑offs.*

---

## 3. Bad Things About Two‑Sum

| # | Why it can be *bad* or *error‑prone* |
|---|-------------------------------------|
| 1 | **“Exactly one answer” is a hidden promise** – Some solutions assume “at least one” and don’t handle “no answer” (e.g., return an empty array). |
| 2 | **Duplicate values** – A naïve hash map can silently overwrite indices, and a two‑loop version can double‑count. |
| 3 | **Index vs. value confusion** – It’s easy to accidentally return the *value* instead of the *index*. |
| 4 | **O(n²) solutions survive but are sub‑optimal** – Many beginners hand‑write the nested loop, which works but doesn’t demonstrate algorithmic thinking. |
| 5 | **Edge‑case “same element twice”** – For input `[3,3]`, a solution that simply looks for `target - nums[i]` and returns immediately will mistakenly return `[0,0]`. |

> *In short: the problem is too easy for some, but too easy for others if you ignore the constraints.*

---

## 4. Ugly Things About Two‑Sum

| # | What can go terribly wrong? |
|---|-----------------------------|
| 1 | **Hash collisions** – A poorly chosen hash function can degrade the map from *amortized* O(1) to O(n). In practice this rarely happens, but it’s a theoretical pitfall. |
| 2 | **Unbounded memory** – If the array size is close to the upper limit, a hash map that stores every element can become memory‑heavy. |
| 3 | **Mutable array pitfalls** – Using sorting with indices can corrupt the original order if you don’t keep a copy of indices. |
| 4 | **Return value placeholder** – Returning `new int[2]` on failure (as in the posted code) hides bugs: the caller sees `[0,0]` even though there’s *no* solution. |
| 5 | **Java generics & boxing** – Using `Map<Integer, Integer>` incurs autoboxing overhead; `Int2IntOpenHashMap` from fastutil or Trove’s `TIntIntHashMap` can be more efficient for large inputs. |

> *These ugliness points are rarely discussed, but a production‑grade implementation should be aware of them.*

---

## 5. A Clean, One‑Pass Hash‑Table Solution

Below is a minimal, robust implementation in Java that tackles the good, bad, and ugly points:

```java
import java.util.*;

public class TwoSumSolution {

    public int[] twoSum(int[] nums, int target) {
        // A map from number -> its index.
        Map<Integer, Integer> indexMap = new HashMap<>();

        for (int i = 0; i < nums.length; i++) {
            int complement = target - nums[i];

            // 1️⃣ Check if we've already seen the complement.
            Integer j = indexMap.get(complement);
            if (j != null) {
                // 2️⃣ Return the two indices. The order is irrelevant.
                return new int[]{j, i};
            }

            // 3️⃣ Store the current number so future elements can find it.
            indexMap.put(nums[i], i);
        }

        // The problem guarantees exactly one solution, but throw an exception
        // to surface bugs early if the guarantee ever changes.
        throw new IllegalArgumentException("No two sum solution exists.");
    }
}
```

### Why this is *good*:

- **One pass** → O(n) time, no duplicate loops.
- **Early exit** → We never do a second iteration.
- **No self‑pair** → The check happens *before* we insert the current value.
- **Clear intent** → Commented steps make the logic obvious.
- **Safe failure** → An exception instead of a silent “[0,0]”.

### What it hides (and why it’s acceptable):

- *Hash map collisions*: We rely on Java’s `HashMap`, which is robust for the given constraints. If you need absolute guarantees, use a custom open‑addressing map.
- *Boxing overhead*: For LeetCode's time limits this is negligible; in production you might switch to an int‑specific map.

---

## 6. Alternative Approaches & When to Use Them

| Approach | Time | Space | Use‑case |
|----------|------|-------|----------|
| Brute‑force (nested loops) | O(n²) | O(1) | Very small arrays or teaching basic loops |
| Two‑pass Hash Map | O(n) | O(n) | Simple, clear two‑phase logic |
| One‑pass Hash Map | O(n) | O(n) | Most interview‑ready solution |
| Sorting + Two‑pointer | O(n log n) | O(1) or O(n) | When you’re allowed to modify the array or need sorted output |
| Binary Search (after sort) | O(n log n) | O(1) | Smallish arrays; not ideal if you need indices |

> *Sorting + two‑pointer is elegant if you can tolerate changing the order of the input or if you’re returning values rather than indices. In interviews, the hash‑map approach is usually expected.*

---

## 7. Interview Tips

1. **Ask clarifying questions**: “Do the indices need to be returned in ascending order?” “Can `nums` contain duplicates?” “What if no pair exists?”
2. **Explain the hash‑table idea upfront**: “We’ll store each number’s index and look for its complement as we iterate.”
3. **Discuss time & space trade‑offs**: “We’re trading O(n) space for O(n) time.”
4. **Mention edge‑case handling**: “We’ll throw an exception if no pair is found to guard against invalid input.”
5. **Talk about robustness**: “If we needed to support millions of entries, we could switch to a specialized primitive map to avoid autoboxing.”

---

## 8. Final Takeaway

Two‑Sum is a deceptively simple interview staple that invites a deep dive into:

- **Algorithmic elegance**: From brute‑force to a one‑pass hash map.
- **Edge‑case safety**: Handling duplicates, self‑pairs, and guaranteed solutions.
- **Code quality**: Readability, defensive programming, and performance nuances.

When you master this problem, you’ve also learned how to balance **time‑complexity** against **space‑complexity**, how to write **clear** and **robust** Java code, and how to anticipate the *ugly* pitfalls that can trip up even seasoned programmers.  

Happy coding!
