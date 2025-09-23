---
title: LeetCode 2531. Make Number of Distinct Characters Equal - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Short answer** – yes.  
You can decide the answer in *O(|w1|+|w2|)* time by only examining the
frequency of each letter (there are only 26 of them).  
The key idea is to track the *number of unique letters* in each string
(`c1` and `c2`) and see what happens to those numbers when you swap two
letters.

---

## Why brute‑force is unnecessary

If we literally tried every pair of positions in `w1` and `w2` we would
need

```
O(|w1| · |w2|)
```

operations.  
Because a swap can only affect the *type* of the two letters involved
(`a` from `w1` and `b` from `w2`), the effect depends only on the
*frequency* of `a` in both strings and of `b` in both strings.
There are only 26 possible letters, so we can try all `26 × 26` pairs
and compute the effect in constant time.

---

## What changes when we swap `a` (from `w1`) with `b` (from `w2`)

| Situation | Effect on the *unique‑letter* count of the string it leaves |
|-----------|-------------------------------------------------------------|
| `a` appears **once** in `w1` → it becomes unique after we take it out → **c1–=1** |
| `a` does **not** appear in `w2` → it becomes a new unique letter in `w2` → **c2+=1** |
| `b` appears **once** in `w2` → it becomes unique after we take it out → **c2–=1** |
| `b` does **not** appear in `w1` → it becomes a new unique letter in `w1` → **c1+=1** |

If `a` and `b` are the *same* character the first two changes cancel
each other out – we actually end up with the same `c1` and `c2` as we
started with, so this swap can only succeed when `c1 == c2` already.

With these rules we can compute the *new* number of unique letters
without touching the actual strings.

---

## Java implementation (O(|w1|+|w2|) time, O(1) space)

```java
import java.util.*;

public class Solution {

    public boolean isItPossible(String w1, String w2) {
        // Frequency of each letter
        int[] f1 = new int[26];
        int[] f2 = new int[26];

        // Count unique letters in each string
        int uniq1 = 0, uniq2 = 0;
        for (char c : w1.toCharArray()) {
            if (f1[c - 'a']++ == 0) uniq1++;
        }
        for (char c : w2.toCharArray()) {
            if (f2[c - 'a']++ == 0) uniq2++;
        }

        /* Early exit: if the difference is > 2, one swap can change
           the count by at most 2 (remove a unique char and/or add a
           new unique char).  If it's impossible to bring the two counts
           within 2, the answer is definitely false. */
        if (Math.abs(uniq1 - uniq2) > 2) return false;

        /* Try every pair (a from w1, b from w2) */
        for (int a = 0; a < 26; a++) {
            if (f1[a] == 0) continue;            // a must exist in w1

            // copy current unique counts for this a
            int newUniq1 = uniq1;
            int newUniq2 = uniq2;

            // Removing a from w1
            if (f1[a] == 1) newUniq1--;           // a was unique
            if (f2[a] == 0) newUniq2++;           // a is new in w2

            for (int b = 0; b < 26; b++) {
                if (f2[b] == 0) continue;        // b must exist in w2

                int curUniq1 = newUniq1;
                int curUniq2 = newUniq2;

                // If a == b we swap the same letter
                if (a == b) {
                    // In this case we already handled the effect of
                    // removing a from w1, adding it to w2.  No change
                    // to the unique counts – we only need to check
                    // whether the current counts are already equal.
                    if (curUniq1 == curUniq2) return true;
                    continue;
                }

                // Adding b to w1
                if (f1[b] == 0) curUniq1++;        // b is new in w1

                // Removing b from w2
                if (f2[b] == 1) curUniq2--;       // b was unique

                /* Now curUniq1 / curUniq2 are the unique counts after
                   swapping a with b.  If they match, we have found a
                   valid swap. */
                if (curUniq1 == curUniq2) return true;
            }
        }
        return false;   // no pair succeeded
    }
}
```

### How it works – step by step

1. **Build frequency tables** – O(|w1|+|w2|).  
   `f1[i]` is the number of times letter `'a'+i` appears in `w1`;  
   `f2[i]` does the same for `w2`.

2. **Count the unique letters** – a letter is unique if its frequency
   is exactly 1.  
   This gives us `uniq1` and `uniq2`.

3. **Early pruning** – if `|uniq1 – uniq2| > 2`, we can return `false`
   immediately.

4. **Enumerate all 26×26 pairs** – for each `a` that appears in `w1`
   and each `b` that appears in `w2` we compute the effect on the two
   unique‑letter counts using the table above.  
   All operations inside the loops are constant time, so the total
   time is `26 × 26 = 676`, i.e. **O(1)** with respect to the string
   lengths.

5. **Return** – the first pair that yields equal counts is a success;
   if we exhaust all pairs we return `false`.

---

## Complexity

- **Time** – `O(|w1| + |w2|)` for building the frequency tables, plus
  the constant 26×26 loop (≈ 676 steps).  
  So the overall running time is linear in the input size.

- **Space** – we use two arrays of length 26, i.e. `O(1)` additional
  memory.

---

## Takeaway

You don’t need to try every position in the strings.  
Because only the *letter types* matter for the unique‑letter counts,
you can analyse the effect of a single swap in constant time and
try all `26 × 26` possible letter pairs.  The result is an
O(|w1|+|w2|) algorithm that is both simple to implement and easy to
understand.