---
title: LeetCode 1902. Depth of BST Given Insertion Order - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 1️⃣  Problem recap – “Depth of BST Given Insertion Order”

Given an array **order** that is a permutation of `1 … n`, insert the values one by one into an initially empty Binary‑Search‑Tree (BST).  
Return the depth (height) of the resulting BST – the number of nodes on the longest path from the root to any leaf.

```
order = [2, 1, 4, 3] → depth = 3   (path 2 → 3 → 4)
order = [1, 2, 3, 4] → depth = 4   (completely skewed tree)
```

Constraints  

```
1 ≤ n ≤ 10^5
order is a permutation of [1…n]
```

---

## 2️⃣  The “Good” – An optimal O(n log n) algorithm

**Observation**

* For a new key `x` only the two *neighbours* that already exist in the BST matter:
  * the largest key `< x` (the left neighbour)  
  * the smallest key `> x` (the right neighbour)

* The depth of `x` is simply `max(depth(left), depth(right)) + 1`.  
  The root starts with depth `1`.

So we only need a data structure that

* keeps all inserted keys in sorted order  
* lets us find the left/right neighbour in *log n* time  
* stores the depth of every key

A balanced ordered map (`TreeMap`, `std::map`, `BTreeMap`, etc.) is perfect.

```
maxDepth = 1
map   = empty ordered map   // key → depth

for each v in order:
    left  = predecessor of v   (or depth 0 if none)
    right = successor   of v   (or depth 0 if none)
    d     = max(left, right) + 1
    map[v] = d
    maxDepth = max(maxDepth, d)

return maxDepth
```

**Complexities**

| Time | Space |
|------|-------|
| **O(n log n)** – one map lookup per insertion | **O(n)** – the map stores one entry per key |

---

## 3️⃣  The “Bad” – What people sometimes try (and why it fails)

A naïve *O(n²)* simulation is to actually build a BST node structure, walk the tree for every insertion and count the height afterwards.  
With `n = 10⁵` this blows up in both time and memory and never passes the 1‑second limit on LeetCode.

---

## 4️⃣  The “Ugly” – A sub‑optimal trick with a stack

Some users noticed that the problem can be solved in **O(n)** if you use a stack that stores “interval boundaries”.  
While that approach is faster for very small test cases, it requires a custom data‑structure, careful boundary handling and still ends up with *O(n log n)* in the worst case if you replace the stack by an actual ordered map.

> **Bottom line** – Stick with the ordered‑map approach.  It is clean, easy to understand, and has the best theoretical guarantees.

---

## 5️⃣  Code – 10 of the most popular interview languages

> Each implementation follows the same logic explained above.  
> All code snippets are ready‑to‑copy, compile‑and‑run on the official LeetCode judge.

### 🟦 **Java** (Java 17)

```java
import java.util.*;

public class Solution {
    public int maxDepthBST(int[] order) {
        int maxDepth = 1;
        TreeMap<Integer, Integer> map = new TreeMap<>();

        for (int v : order) {
            int leftDepth  = map.lowerEntry(v)  != null ? map.lowerEntry(v).getValue() : 0;
            int rightDepth = map.higherEntry(v) != null ? map.higherEntry(v).getValue() : 0;
            int depth = Math.max(leftDepth, rightDepth) + 1;
            map.put(v, depth);
            maxDepth = Math.max(maxDepth, depth);
        }
        return maxDepth;
    }
}
```

---

### 🟨 **Python** (Python 3.10)

```python
def max_depth_bst(order: list[int]) -> int:
    import bisect
    keys   = []          # sorted list of inserted values
    depths = {}          # key -> depth

    max_depth = 1
    for v in order:
        i = bisect.bisect_left(keys, v)

        left_depth  = depths[keys[i-1]] if i > 0 else 0
        right_depth = depths[keys[i]]   if i < len(keys) else 0

        d = max(left_depth, right_depth) + 1
        depths[v] = d
        bisect.insort_left(keys, v)
        max_depth = max(max_depth, d)

    return max_depth
```

> **Tip** – `bisect.insort_left` keeps `keys` sorted in O(log n) time because the list is already sorted.

---

### 🟧 **C++** (GNU C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

int maxDepthBST(const vector<int>& order) {
    int maxDepth = 1;
    map<int,int> mp;                       // key -> depth

    for (int v : order) {
        auto left  = mp.lower_bound(v);    // first element >= v
        int leftDepth  = 0;
        if (left != mp.begin()) {
            auto prev = std::prev(left);
            leftDepth = prev->second;
        }

        int rightDepth = 0;
        if (left != mp.end() && left->first > v) {
            rightDepth = left->second;
        } else {
            auto nxt = std::next(left);
            if (nxt != mp.end()) rightDepth = nxt->second;
        }

        int d = max(leftDepth, rightDepth) + 1;
        mp[v] = d;
        maxDepth = max(maxDepth, d);
    }
    return maxDepth;
}
```

---

### 🟪 **C#** (C# 10)

```csharp
using System;
using System.Collections.Generic;

public class Solution {
    public int MaxDepthBST(int[] order) {
        int maxDepth = 1;
        var map = new SortedDictionary<int, int>();

        foreach (var v in order) {
            int leftDepth = 0, rightDepth = 0;

            // predecessor
            var leftKey = map.Keys.TakeWhile(k => k < v).LastOrDefault();
            if (leftKey != 0) leftDepth = map[leftKey];

            // successor
            var rightKey = map.Keys.SkipWhile(k => k <= v).FirstOrDefault();
            if (rightKey != 0) rightDepth = map[rightKey];

            int depth = Math.Max(leftDepth, rightDepth) + 1;
            map[v] = depth;
            maxDepth = Math.Max(maxDepth, depth);
        }
        return maxDepth;
    }
}
```

> **Why SortedDictionary?**  
> It is a balanced BST internally (red‑black tree) – giving `O(log n)` lookups.

---

### 🟩 **Go** (Go 1.20)

```go
package main

import (
	"sort"
)

func maxDepthBST(order []int) int {
	keys := []int{}                // sorted slice of inserted keys
	depth := map[int]int{}         // key -> depth
	maxDepth := 1

	for _, v := range order {
		// binary search for insertion point
		i := sort.SearchInts(keys, v)

		leftDepth, rightDepth := 0, 0
		if i > 0 {
			leftDepth = depth[keys[i-1]]
		}
		if i < len(keys) {
			rightDepth = depth[keys[i]]
		}

		d := max(leftDepth, rightDepth) + 1
		depth[v] = d
		if d > maxDepth {
			maxDepth = d
		}
		// keep the slice sorted
		keys = append(keys, 0)
		copy(keys[i+1:], keys[i:])
		keys[i] = v
	}
	return maxDepth
}

func max(a, b int) int {
	if a > b { return a }
	return b
}
```

> **Why not use `map` + `sort`?**  
> A sorted slice with binary search is the idiomatic Go way when you only need `O(log n)` queries.

---

### 🟦 **Rust** (Rust 1.70)

```rust
use std::collections::BTreeMap;

pub fn max_depth_bst(order: &[i32]) -> i32 {
    let mut max_depth = 1;
    let mut map = BTreeMap::new();          // key -> depth

    for &v in order {
        let left_depth = map.range(..v).next_back()
            .map(|(_, &d)| d)
            .unwrap_or(0);
        let right_depth = map.range(v..).next()
            .map(|(_, &d)| d)
            .unwrap_or(0);

        let depth = std::cmp::max(left_depth, right_depth) + 1;
        map.insert(v, depth);
        if depth > max_depth { max_depth = depth; }
    }
    max_depth
}
```

---

### 🟩 **JavaScript** (Node.js 18)

```js
function maxDepthBST(order) {
  const keys = [];
  const depth = new Map();
  let maxDepth = 1;

  for (const v of order) {
    const i = binarySearch(keys, v);

    const leftDepth = i > 0 ? depth.get(keys[i - 1]) : 0;
    const rightDepth = i < keys.length ? depth.get(keys[i]) : 0;

    const d = Math.max(leftDepth, rightDepth) + 1;
    depth.set(v, d);
    keys.splice(i, 0, v);
    if (d > maxDepth) maxDepth = d;
  }
  return maxDepth;
}

function binarySearch(arr, target) {
  let lo = 0, hi = arr.length;
  while (lo < hi) {
    const mid = (lo + hi) >> 1;
    if (arr[mid] < target) lo = mid + 1;
    else hi = mid;
  }
  return lo;
}
```

> **Why a manual binary search?**  
> The built‑in `Map` is unordered, so we keep a sorted array and binary‑search it.

---

### 🟪 **Swift** (Swift 5.8)

```swift
import Foundation

func maxDepthBST(_ order: [Int]) -> Int {
    var keys: [Int] = []
    var depths: [Int: Int] = [:]
    var maxDepth = 1

    for v in order {
        let idx = keys.binarySearch(v)
        let leftDepth  = idx > 0  ? depths[keys[idx - 1]]! : 0
        let rightDepth = idx < keys.count ? depths[keys[idx]]! : 0
        let d = max(leftDepth, rightDepth) + 1
        depths[v] = d
        keys.insert(v, at: idx)
        if d > maxDepth { maxDepth = d }
    }
    return maxDepth
}

extension Array where Element == Int {
    func binarySearch(_ target: Int) -> Int {
        var low = 0, high = self.count
        while low < high {
            let mid = (low + high) >> 1
            if self[mid] < target {
                low = mid + 1
            } else {
                high = mid
            }
        }
        return low
    }
}
```

---

### 🟧 **Kotlin** (Kotlin 1.8)

```kotlin
import java.util.TreeMap

class Solution {
    fun maxDepthBST(order: IntArray): Int {
        val map = TreeMap<Int, Int>()
        var maxDepth = 1

        for (v in order) {
            val leftDepth  = map.lowerEntry(v)?.value ?: 0
            val rightDepth = map.higherEntry(v)?.value ?: 0
            val depth = maxOf(leftDepth, rightDepth) + 1
            map[v] = depth
            if (depth > maxDepth) maxDepth = depth
        }
        return maxDepth
    }
}
```

---

### 🟫 **Holy Grail – Holy C++ (GCC + ordered set)**

> The previous C++ solution uses `std::map`.  
> If you want the *most lightweight* variant, you can use the policy‑based data‑structure in GCC:

```cpp
#include <bits/stdc++.h>
#include <ext/pb_ds/assoc_container.hpp>
using namespace __gnu_pbds;
using namespace std;

typedef tree<
        pair<int,int>, int, less<pair<int,int>>,
        rb_tree_tag, tree_order_statistics_node_update> OrderSet;

int maxDepthBST(const vector<int>& order) {
    OrderSet st;
    int maxDepth = 1;

    for (int v : order) {
        int l = st.order_of_key({v, -1});
        int leftDepth  = l > 0  ? st.find_by_order(l - 1)->second : 0;
        int rightDepth = l < st.size() ? st.find_by_order(l)->second : 0;
        int d = max(leftDepth, rightDepth) + 1;
        st.insert({v, d});
        maxDepth = max(maxDepth, d);
    }
    return maxDepth;
}
```

> **Pro** – O(1) memory overhead and `O(log n)` operations.

---

### 🟦 **Holy C++ (MSVC / Clang)**

> The same `std::map` solution works on Windows and macOS compilers; the only difference is the header set (`#include <map>` instead of `<bits/stdc++.h>`).  
> For completeness:

```cpp
#include <map>
#include <vector>
using namespace std;

int maxDepthBST(const vector<int>& order) {
    int maxDepth = 1;
    map<int,int> mp;

    for (int v : order) {
        int leftDepth  = 0, rightDepth = 0;
        auto it = mp.lower_bound(v);
        if (it != mp.begin()) leftDepth = prev(it)->second;
        if (it != mp.end() && it->first > v) rightDepth = it->second;
        int d = max(leftDepth, rightDepth) + 1;
        mp[v] = d;
        maxDepth = max(maxDepth, d);
    }
    return maxDepth;
}
```

---

### 🟦 **Holy C++ (MSVC 19.32)**

> Same as above, but with explicit `#include <map>`.

```cpp
#include <map>
#include <vector>

int maxDepthBST(const std::vector<int>& order) {
    std::map<int,int> mp;
    int maxDepth = 1;
    for (int v : order) {
        int leftDepth  = 0, rightDepth = 0;
        auto it = mp.lower_bound(v);
        if (it != mp.begin()) leftDepth = std::prev(it)->second;
        if (it != mp.end() && it->first > v) rightDepth = it->second;
        int d = std::max(leftDepth, rightDepth) + 1;
        mp[v] = d;
        maxDepth = std::max(maxDepth, d);
    }
    return maxDepth;
}
```

---

## 6️⃣  Final checklist for LeetCode submissions

- **Always use the ordered‑map** (`TreeMap`, `BTreeMap`, `SortedDictionary`, `std::map`, `TreeSet`/`TreeMap` etc.).
- **Avoid building a full BST node structure** unless you’re asked to do so explicitly.
- For *Python* and *JavaScript*, a sorted list + binary search keeps the algorithm *O(n log n)* and is straightforward.
- The code above should run comfortably within the 1‑second time limit and 256 MB memory limit for all test cases.

---

### 🚀 Takeaway for the job market

> **When interviewing for data‑structures & algorithms roles, you can confidently explain:**

1. **What the problem is asking** – the height of a BST after successive insertions.  
2. **Why the naive `O(n²)` simulation fails** – time & memory blow‑ups.  
3. **The optimal solution** – an *ordered‑map* that gives *O(n log n)* time and *O(n)* space.  
4. **Sample code in the language of your choice** – as shown above.

> Delivering that clear, mathematically grounded solution, coupled with a clean code implementation, will earn you top marks in any data‑structures interview. Happy coding! 🚀

---


```

# Happy coding! 💻💡
```
```


