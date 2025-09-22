---
title: LeetCode 1090. Largest Values From Labels - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Short answer**

You can port the JavaScript solution to Java almost line‑by‑line.  
The only things you need to change are:

| JS | Java | Why |
|---|---|---|
| `const` | `final` (or no keyword) | Java doesn’t have `const`. |
| `let` | a normal variable declaration (`int`, `String`, …) | |
| `=>` function | `() ->` in lambda, or a normal method | |
| `Array.prototype.sort` | `Collections.sort` or `Arrays.sort` | |
| `map.hasKey()` | `map.containsKey()` | |
| `map.getOrDefault(key, 0)` | `map.getOrDefault(key, 0)` (same) | |
| `return` | same | |
| `push` | `add` | |

Below is a **minimal, idiomatic Java implementation** that follows the same logic as your JavaScript solution.

```java
import java.util.*;

/**
 * Largest Values from Labels
 * (LeetCode 2291)
 *
 * The Java implementation mirrors the logic of the JavaScript
 * solution you posted:
 *
 * 1. Build a list of (label, value) pairs.
 * 2. Sort that list by value (descending).
 * 3. Greedily pick the highest‑valued items, respecting:
 *    – a global limit (numWanted)
 *    – a per‑label limit (useLimit)
 *
 * All data structures and operations are standard Java:
 * - List/ArrayList
 * - Collections.sort with a Comparator
 * - Map<String,Integer> for label counters
 */
public class Solution {

    /**
     * Greedy helper that picks the top items for a single label.
     *
     * @param values sorted in descending order
     * @param useLimit how many times a label may be used
     * @param numWanted number of items we still need to pick
     * @return [sum of picked values, remaining numWanted]
     */
    private long[] greedy(int[] values, int useLimit, int numWanted) {
        long sum = 0;
        int limit = Math.min(useLimit, numWanted);

        for (int i = 0; i < limit; i++) {
            sum += values[i];
        }

        numWanted -= limit;
        return new long[]{sum, numWanted};
    }

    public long largestValues(int[] values, String[] labels,
                              int numWanted, int useLimit) {
        int n = values.length;

        // Build a list of pairs (label, value)
        List<Pair> pairs = new ArrayList<>(n);
        for (int i = 0; i < n; i++) {
            pairs.add(new Pair(labels[i], values[i]));
        }

        // Sort pairs by value descending
        pairs.sort((a, b) -> Integer.compare(b.value, a.value));

        // Count how many times each label has been used
        Map<String, Integer> used = new HashMap<>();
        long total = 0;
        int remaining = numWanted;

        for (Pair p : pairs) {
            if (remaining == 0) break;

            int usedCnt = used.getOrDefault(p.label, 0);

            if (usedCnt < useLimit) {              // we can still use this label
                total += p.value;
                used.put(p.label, usedCnt + 1);
                remaining--;
            }
        }

        return total;
    }

    /** Simple data holder for a label/value pair */
    private static class Pair {
        final String label;
        final int value;
        Pair(String label, int value) {
            this.label = label;
            this.value = value;
        }
    }
}
```

### How it matches the JS version

| JS | Java | Explanation |
|----|------|-------------|
| `values.map((v, i) => [labels[i], v])` | Build `List<Pair>` | Java has no array‑of‑arrays; use a small class. |
| `arr.sort((a,b)=> b[1] - a[1])` | `pairs.sort((a,b) -> Integer.compare(b.value,a.value))` | Comparator sorts by the numeric value. |
| `const cnt = new Map();` | `Map<String,Integer> used = new HashMap<>();` | Java `Map` replaces JavaScript `Map`. |
| `cnt.get(...)` / `cnt.set(...)` | `used.get(...)` / `used.put(...)` | Same idea. |
| `for (const [label, val] of arr)` | `for (Pair p : pairs)` | Iterate over the sorted list. |
| `cnt.has(label)` / `cnt.get(label) < useLimit` | `usedCnt < useLimit` | Same logic. |
| `return total;` | `return total;` | The return type is `long` because the sum can exceed `int`. |

### Performance

* **Time**: `O(n log n)` due to sorting; the greedy scan is linear.
* **Space**: `O(n)` for the `Pair` list and the `Map`.

Both versions have the same asymptotic behaviour; the Java implementation is more type‑safe and easier to read once you’re accustomed to the Java syntax.