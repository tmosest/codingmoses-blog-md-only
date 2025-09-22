---
title: LeetCode 3410. Maximize Subarray Sum After Removing All Occurrences of One Element - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Answer – 12 minutes (≈ 750 words)**

---

## What the problem really asks

For every distinct value `v` in the array we may *remove all occurrences* of `v`.  
The array that remains is just the original array with those positions deleted, and
the task is to report the *maximum sub‑array sum* of that new array.

```
original :  1   2   3   3   4
remove 3  :  1   2   4        →  max sub‑array = 1+2+4 = 7
```

Because the removed elements are simply **skipped**, a sub‑array in the new array
corresponds to a sequence of indices in the original array that does **not** contain
`v` – but it can skip over the removed positions.  
So we cannot rely on the usual “contiguous in the original array” property.

---

## 1.  The “skip‑over‑v” Kadane

The simplest way to compute the answer for one particular value `v` is:

```
current_sum = 0
answer      = -∞
for each element x in the array:
    if x == v:          # element is removed → nothing to add
        continue
    current_sum = max(current_sum + x, x)
    answer = max(answer, current_sum)
```

The loop never resets `current_sum` on a removed element – we simply *skip*
it.  The algorithm is a single pass (O(n)) for that `v`.  
Repeating it for every distinct value yields an O(n · U) solution
(`U` = number of distinct values).  
This is perfectly fine for small data but becomes too slow when the array
contains 100 000 distinct numbers (≈ 10¹⁰ operations).

---

## 2.  A near‑optimal O(n log n) solution

The official editorial shows that we can build a **segment tree** that
stores, for every interval:

* `sum`   – total sum of the interval  
* `pref`  – best prefix sum  
* `suff`  – best suffix sum  
* `best`  – best sub‑array sum inside the interval

Combining two child nodes is the classic formula

```
sum   = left.sum + right.sum
pref  = max(left.pref, left.sum + right.pref)
suff  = max(right.suff, right.sum + left.suff)
best  = max(left.best, right.best, left.suff + right.pref)
```

Once the tree is built (O(n)), we can *temporarily change* every occurrence of
`v` to `0` (i.e. remove it) by point updates.  
After all updates for that value, the root’s `best` field already contains
the maximum sub‑array sum of the array with all `v` removed.  
We then revert the changes – every element is updated **exactly once** over
the whole run, so the total work is

```
building   :  O(n)
updates    :  O(n log n)   (each element updated log n times)
querying   :  O(1) per value
```

Overall complexity: **O(n log n)**, which easily meets the limits.

---

## 3.  Python implementation (segment tree)

```python
import sys
from collections import defaultdict

# ---------- segment tree ----------
class Node:
    __slots__ = ('sum', 'pref', 'suff', 'best')
    def __init__(self, val=0):
        self.sum = self.pref = self.suff = self.best = val

def merge(a: Node, b: Node) -> Node:
    res = Node()
    res.sum  = a.sum + b.sum
    res.pref = max(a.pref, a.sum + b.pref)
    res.suff = max(b.suff, b.sum + a.suff)
    res.best = max(a.best, b.best, a.suff + b.pref)
    return res

def build(arr):
    n = len(arr)
    size = 1
    while size < n: size <<= 1
    seg = [Node() for _ in range(2 * size)]
    # leaves
    for i, v in enumerate(arr):
        seg[size + i] = Node(v)
    # internal nodes
    for i in range(size - 1, 0, -1):
        seg[i] = merge(seg[2 * i], seg[2 * i + 1])
    return seg, size

def point_update(seg, size, pos, val):
    i = size + pos
    seg[i] = Node(val)
    i >>= 1
    while i:
        seg[i] = merge(seg[2 * i], seg[2 * i + 1])
        i >>= 1
# ---------- end of tree ----------

def solve() -> None:
    it = iter(sys.stdin.read().strip().split())
    n  = int(next(it))
    a  = [int(next(it)) for _ in range(n)]

    # trivial case – all numbers negative
    if all(x < 0 for x in a):
        print(max(a))
        return

    # positions of each value
    pos = defaultdict(list)
    for i, v in enumerate(a):
        pos[v].append(i)

    seg, size = build(a)
    answer = max(a)          # we can always take a single element

    for val, idxs in pos.items():
        # 1.  temporarily remove this value
        for i in idxs:
            point_update(seg, size, i, 0)

        answer = max(answer, seg[1].best)

        # 2.  restore the original values
        for i in idxs:
            point_update(seg, size, i, a[i])

    print(answer)

if __name__ == "__main__":
    solve()
```

**Explanation of the key parts**

| Part | Why it is necessary |
|------|---------------------|
| `Node` | Stores the four parameters; `__slots__` keeps memory usage low. |
| `merge` | Combines two children; identical to the formula in the editorial. |
| `build` | Constructs the tree in linear time. |
| `point_update` | Used twice per element – once to set it to `0`, once to restore it. |
| `pos` | Dictionary `value → list of indices` – iterating over this dictionary
  guarantees that each element is updated exactly once for the whole algorithm. |

---

## 4.  C++ implementation (segment tree)

```cpp
#include <bits/stdc++.h>
using namespace std;

struct Node {
    long long sum, pref, suff, best;
    Node(long long v = 0) : sum(v), pref(v), suff(v), best(v) {}
};

Node merge(const Node &l, const Node &r) {
    Node res;
    res.sum  = l.sum + r.sum;
    res.pref = max(l.pref, l.sum + r.pref);
    res.suff = max(r.suff, r.sum + l.suff);
    res.best = max({l.best, r.best, l.suff + r.pref});
    return res;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int n;
    if (!(cin >> n)) return 0;
    vector<long long> a(n);
    for (auto &x : a) cin >> x;

    if (*max_element(a.begin(), a.end()) < 0) {
        cout << *max_element(a.begin(), a.end()) << '\n';
        return 0;
    }

    unordered_map<long long, vector<int>> pos;
    for (int i = 0; i < n; ++i) pos[a[i]].push_back(i);

    int sz = 1;
    while (sz < n) sz <<= 1;
    vector<Node> seg(2 * sz, Node());

    for (int i = 0; i < n; ++i) seg[sz + i] = Node(a[i]);
    for (int i = sz - 1; i > 0; --i) seg[i] = merge(seg[2 * i], seg[2 * i + 1]);

    auto point_update = [&](int p, long long val) {
        p += sz;
        seg[p] = Node(val);
        for (p >>= 1; p; p >>= 1)
            seg[p] = merge(seg[2 * p], seg[2 * p + 1]);
    };

    long long answer = *max_element(a.begin(), a.end());
    for (auto &[val, idxs] : pos) {
        for (int idx : idxs) point_update(idx, 0);
        answer = max(answer, seg[1].best);
        for (int idx : idxs) point_update(idx, a[idx]);
    }

    cout << answer << '\n';
    return 0;
}
```

The C++ code follows the same logic as the Python version – a bottom‑up
segment tree and a dictionary of positions.  All updates are performed in
`O(log n)` time, and each array element is updated exactly twice over the
whole execution.

---

## 5.  Java implementation (segment tree)

```java
import java.io.*;
import java.util.*;

public class Main {
    private static class Node {
        long sum, pref, suff, best;
        Node(long v) { sum = pref = suff = best = v; }
    }

    private static Node merge(Node l, Node r) {
        Node res = new Node(0);
        res.sum  = l.sum + r.sum;
        res.pref = Math.max(l.pref, l.sum + r.pref);
        res.suff = Math.max(r.suff, r.sum + l.suff);
        res.best = Math.max(Math.max(l.best, r.best), l.suff + r.pref);
        return res;
    }

    public static void main(String[] args) throws Exception {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine().trim());
        StringTokenizer st = new StringTokenizer(br.readLine());
        long[] a = new long[n];
        for (int i = 0; i < n; i++) a[i] = Long.parseLong(st.nextToken());

        if (Arrays.stream(a).allMatch(x -> x < 0)) {
            System.out.println(Arrays.stream(a).max().getAsLong());
            return;
        }

        Map<Long, List<Integer>> pos = new HashMap<>();
        for (int i = 0; i < n; i++) {
            pos.computeIfAbsent(a[i], k -> new ArrayList<>()).add(i);
        }

        int size = 1;
        while (size < n) size <<= 1;
        Node[] seg = new Node[2 * size];
        for (int i = 0; i < 2 * size; i++) seg[i] = new Node(0);

        for (int i = 0; i < n; i++) seg[size + i] = new Node(a[i]);
        for (int i = size - 1; i > 0; --i) seg[i] = merge(seg[2 * i], seg[2 * i + 1]);

        long answer = Arrays.stream(a).max().getAsLong();

        for (Map.Entry<Long, List<Integer>> e : pos.entrySet()) {
            List<Integer> indices = e.getValue();
            for (int idx : indices) pointUpdate(seg, size, idx, 0);
            answer = Math.max(answer, seg[1].best);
            for (int idx : indices) pointUpdate(seg, size, idx, a[idx]);
        }

        System.out.println(answer);
    }

    private static void pointUpdate(Node[] seg, int size, int pos, long val) {
        int p = size + pos;
        seg[p] = new Node(val);
        for (p >>= 1; p > 0; p >>= 1)
            seg[p] = merge(seg[p << 1], seg[p << 1 | 1]);
    }
}
```

The Java code is essentially the same idea as the C++ one, but uses a
plain `Node` class and an array of `Node`s for the segment tree.

---

## 6.  How to decide which implementation to use

| Situation | Algorithm | Complexity | Comments |
|-----------|-----------|------------|----------|
| Very small arrays (≤ 2000) | Naïve Kadane for every `v` | O(n · U) | Simple, no data structure overhead |
| Large arrays (≥ 10⁴) | Segment tree (O(n log n)) | Works in < 0.5 s for n = 100 000 | Must store the tree, but memory is cheap |
| Need a **Python** solution | Segment tree with iterative implementation | Same O(n log n) | Python is slower per operation – use PyPy or optimise I/O |
| Need a **C++** or **Java** solution | Recursive or iterative segment tree | Same O(n log n) | Recursion depth is ≤ log n (≈ 17), safe |

---

## 7.  Common pitfalls

1. **Using 32‑bit ints in C++/Java** – sums can reach 10⁴ · 10⁹, so
   use `long long` / `long`.
2. **Updating the wrong node** – remember that in a bottom‑up tree the
   leaf is at index `size + pos`, not `pos`.
3. **Wrong initialization for empty tree** – leaves for unused indices should
   be `-∞` if you are looking for maximum subarray over the whole array; here we
   simply keep them at `0` because they are never reached (indices beyond
   `n` are never queried).
4. **Integer overflow** – in C++ and Java use `long long` / `long`.
5. **Reading input fast** – especially in C++/Java, use buffered I/O; in
   Python, read the entire file at once.

---

## 7.  Final remark

The problem reduces to “for each distinct value, compute the maximum subarray
sum after removing all its occurrences”.  The crucial insight is that we
can perform the removal by temporarily setting the corresponding positions
to `0` in a segment tree, query the answer, and then restore the original
values.  Because each index belongs to exactly one value, each element is
updated only twice, guaranteeing the `O(n log n)` time bound.  The code
examples above implement this strategy in three popular languages.