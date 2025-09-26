---
title: LeetCode 3414. Maximum Score of Non-overlapping Intervals - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 1.  The problem, in a nutshell

We are given **N ≤ 50 000** intervals  

```
[lᵢ , rᵢ , wᵢ]      (1 ≤ lᵢ ≤ rᵢ ≤ 10⁹, 1 ≤ wᵢ ≤ 10⁹)
```

and we have to pick **at most four** of them so that

* no two chosen intervals touch each other (the right end of one must be *strictly* smaller than the left end of the other),
* the total weight is maximal, and  
* if several subsets give the same maximal weight we return the **lexicographically smallest list of original indices** (indices are 0‑based in the order in which the intervals appear in the input).

The required output is the list of original indices in increasing order – that is the list that would be obtained by simply sorting the indices.

> *Why “at most four” matters* – because we can afford a full dynamic‑programming table with an extra dimension **k ∈ {0,…,4}**.  
> *Why the boundary rule matters* – sharing a left or right endpoint is considered overlapping, therefore the *strict* condition `rᵢ < lⱼ` (or vice‑versa) is the one that must hold.

Below you will find three reference solutions, one in **Python**, one in **Java** and one in **C++**. All three solve the problem in **O(N log N)** time and **O(N)** memory – comfortably within the limits.

---

## 2.  The algorithm (same idea in all three languages)

1. **Keep the original indices**  
   Store the 4‑tuple `(l, r, w, idx)` for every interval, where `idx` is the position in the input (0‑based).

2. **Sort by the left endpoint**  
   This gives us a deterministic order in which we will explore the intervals.

3. **Pre‑compute “next” indices**  
   For every interval `i` we need the first interval that starts *after* `rᵢ`.  
   Because touching the end point is forbidden, we search for the smallest `j > i` with  
   `lⱼ > rᵢ`.  
   The binary‑search step uses the sorted list of left ends.

4. **Dynamic programming with an extra dimension `k`**  
   `dp[i][k]` = best (weight, list of chosen indices) achievable starting from interval `i` when we are allowed to take at most `k` more intervals (`k` ∈ {0,1,2,3,4}).  
   The recurrence is the classic “take / skip” choice:

   * **skip** `i` → `dp[i+1][k]`  
   * **take** `i` → `wᵢ + dp[next[i]][k-1]` and append `idxᵢ` to the indices list

   Because the list is always sorted and contains at most 4 numbers, merging / sorting the new list is trivial.

5. **Tie‑breaking**  
   If the two choices give the same total weight we must return the *lexicographically smallest* index list.  
   Lexicographic comparison of two sorted index lists is simply element‑by‑element comparison (e.g. tuple comparison in Python or `std::lexicographical_compare` in C++).

6. **Answer**  
   Start the recursion (or the bottom‑up DP) from `i = 0` and `k = 4`.  
   The resulting list of indices is the required answer.

---

## 3.  Reference implementations

### 3.1  Python 3

```python
#!/usr/bin/env python3
import sys
import bisect
from functools import lru_cache
from typing import List, Tuple

def max_weight_intervals(intervals: List[List[int]]) -> List[int]:
    """
    Returns the lexicographically smallest list of original indices
    that gives the maximum weight using at most 4 non‑overlapping intervals.
    """
    n = len(intervals)
    # Attach original indices and sort by left endpoint
    items = [(l, r, w, idx) for idx, (l, r, w) in enumerate(intervals)]
    items.sort(key=lambda x: x[0])

    starts = [x[0] for x in items]          # sorted left ends
    nxt = [bisect.bisect_right(starts, items[i][1]) for i in range(n)]

    @lru_cache(maxsize=None)
    def dfs(i: int, k: int) -> Tuple[int, Tuple[int, ...]]:
        """Return (score, tuple_of_indices) from position i with at most k picks."""
        if k == 0 or i == n:
            return 0, ()
        # skip
        skip_score, skip_list = dfs(i + 1, k)

        # take
        take_score, take_list = dfs(nxt[i], k - 1)
        take_score += items[i][2]
        new_list = tuple(sorted(take_list + (items[i][3],)))

        # choose best
        if take_score > skip_score:
            return take_score, new_list
        if take_score < skip_score:
            return skip_score, skip_list
        # equal weight → lexicographic comparison
        return take_score, min(skip_list, new_list)

    best_score, best_indices = dfs(0, 4)
    return list(best_indices)


# ---------- driver ----------
if __name__ == "__main__":
    data = sys.stdin.read().strip().splitlines()
    if not data:
        sys.exit()
    N = int(data[0])
    intervals = [list(map(int, line.split())) for line in data[1:N + 1]]
    res = max_weight_intervals(intervals)
    print(" ".join(map(str, res)))
```

**Explanation**

* `@lru_cache` guarantees that every `(i, k)` pair is computed only once.
* The recursion depth is bounded by `N` but the caching prevents a blow‑up: only ~N × 5 states exist.
* `tuple(sorted(...))` guarantees the list is sorted and ready for lexicographic comparison.

---

### 3.2  Java 17

```java
import java.io.*;
import java.util.*;

public class Main {

    private static class Interval {
        int l, r, w, idx;
        Interval(int l, int r, int w, int idx) {
            this.l = l; this.r = r; this.w = w; this.idx = idx;
        }
    }

    private static class Result {
        long score;
        List<Integer> indices; // sorted
        Result(long score, List<Integer> indices) {
            this.score = score;
            this.indices = indices;
        }
    }

    public static List<Integer> solve(List<int[]> raw) {
        int n = raw.size();
        List<Interval> items = new ArrayList<>(n);
        for (int i = 0; i < n; i++) {
            int[] a = raw.get(i);
            items.add(new Interval(a[0], a[1], a[2], i));
        }
        items.sort(Comparator.comparingInt(o -> o.l));

        int[] starts = new int[n];
        for (int i = 0; i < n; i++) starts[i] = items.get(i).l;
        int[] next = new int[n];
        for (int i = 0; i < n; i++) {
            int pos = upperBound(starts, items.get(i).r);
            next[i] = pos;
        }

        long[][] bestScore = new long[n + 1][5];
        @SuppressWarnings("unchecked")
        List<Integer>[][] bestList = new List[n + 1][5];
        for (int k = 0; k <= 4; k++) {
            bestScore[n][k] = 0;
            bestList[n][k] = Collections.emptyList();
        }

        for (int i = n - 1; i >= 0; i--) {
            for (int k = 0; k <= 4; k++) {
                long skipScore = bestScore[i + 1][k];
                List<Integer> skipList = bestList[i + 1][k];

                if (k == 0) {          // nothing left to pick
                    bestScore[i][k] = skipScore;
                    bestList[i][k] = skipList;
                    continue;
                }

                int ni = next[i];
                long takeScore = items.get(i).w + bestScore[ni][k - 1];
                List<Integer> takeList = new ArrayList<>(bestList[ni][k - 1]);
                takeList.add(items.get(i).idx);
                Collections.sort(takeList);

                if (takeScore > skipScore) {
                    bestScore[i][k] = takeScore;
                    bestList[i][k] = takeList;
                } else if (takeScore < skipScore) {
                    bestScore[i][k] = skipScore;
                    bestList[i][k] = skipList;
                } else { // equal weight – lexicographic tie‑break
                    bestScore[i][k] = takeScore;
                    bestList[i][k] = lexicographicMin(skipList, takeList);
                }
            }
        }
        return bestList[0][4];
    }

    // returns the lexicographically smaller of two sorted lists
    private static List<Integer> lexicographicMin(List<Integer> a, List<Integer> b) {
        int i = 0;
        while (i < a.size() && i < b.size()) {
            int va = a.get(i), vb = b.get(i);
            if (va != vb) return va < vb ? a : b;
            i++;
        }
        return a.size() <= b.size() ? a : b;
    }

    // upper bound on a sorted array
    private static int upperBound(int[] arr, int key) {
        int l = 0, r = arr.length;
        while (l < r) {
            int m = (l + r) >>> 1;
            if (arr[m] <= key) l = m + 1;
            else r = m;
        }
        return l;
    }

    // ---------- main ----------
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine().trim());
        List<int[]> raw = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            String[] parts = br.readLine().trim().split("\\s+");
            raw.add(new int[]{Integer.parseInt(parts[0]),
                              Integer.parseInt(parts[1]),
                              Integer.parseInt(parts[2])});
        }
        List<Integer> ans = solve(raw);
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < ans.size(); i++) {
            if (i > 0) sb.append(' ');
            sb.append(ans.get(i));
        }
        System.out.println(sb);
    }
}
```

**Key points**

* `@lru_cache` guarantees O(1) per state.
* `min(skipList, newList)` uses tuple comparison – the lexicographically smaller tuple is automatically chosen when the total weights are equal.

---

### 3.3  Java 17 (bottom‑up DP – no recursion)

```java
import java.io.*;
import java.util.*;

public class Main {
    private static class Interval {
        int l, r, w, idx;
        Interval(int l, int r, int w, int idx) {
            this.l = l; this.r = r; this.w = w; this.idx = idx;
        }
    }

    private static List<Integer> solve(List<int[]> raw) {
        int n = raw.size();
        List<Interval> items = new ArrayList<>(n);
        for (int i = 0; i < n; i++) {
            int[] a = raw.get(i);
            items.add(new Interval(a[0], a[1], a[2], i));
        }
        items.sort(Comparator.comparingInt(o -> o.l));

        int[] starts = new int[n];
        for (int i = 0; i < n; i++) starts[i] = items.get(i).l;
        int[] next = new int[n];
        for (int i = 0; i < n; i++) {
            next[i] = upperBound(starts, items.get(i).r);
        }

        long[][] bestScore = new long[n + 1][5];
        @SuppressWarnings("unchecked")
        List<Integer>[][] bestList = new List[n + 1][5];
        for (int k = 0; k <= 4; k++) {
            bestScore[n][k] = 0;
            bestList[n][k] = Collections.emptyList();
        }

        for (int i = n - 1; i >= 0; i--) {
            for (int k = 0; k <= 4; k++) {
                // skip
                long skipScore = bestScore[i + 1][k];
                List<Integer> skipList = bestList[i + 1][k];

                if (k == 0) {                     // nothing else to take
                    bestScore[i][k] = skipScore;
                    bestList[i][k] = skipList;
                    continue;
                }

                // take
                int ni = next[i];
                long takeScore = items.get(i).w + bestScore[ni][k - 1];
                List<Integer> takeList = new ArrayList<>(bestList[ni][k - 1]);
                takeList.add(items.get(i).idx);
                Collections.sort(takeList);

                if (takeScore > skipScore) {
                    bestScore[i][k] = takeScore;
                    bestList[i][k] = takeList;
                } else if (takeScore < skipScore) {
                    bestScore[i][k] = skipScore;
                    bestList[i][k] = skipList;
                } else {                            // equal weight – lexicographic min
                    bestScore[i][k] = takeScore;
                    bestList[i][k] = lexicographicMin(skipList, takeList);
                }
            }
        }
        return bestList[0][4];
    }

    // upper bound on sorted left endpoints
    private static int upperBound(int[] arr, int key) {
        int l = 0, r = arr.length;
        while (l < r) {
            int m = (l + r) >>> 1;
            if (arr[m] <= key) l = m + 1;
            else r = m;
        }
        return l;
    }

    // returns the lexicographically smaller of two sorted lists
    private static List<Integer> lexicographicMin(List<Integer> a, List<Integer> b) {
        int i = 0;
        while (i < a.size() && i < b.size()) {
            int va = a.get(i), vb = b.get(i);
            if (va != vb) return va < vb ? a : b;
            i++;
        }
        return a.size() <= b.size() ? a : b;
    }

    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        int n = Integer.parseInt(br.readLine().trim());
        List<int[]> raw = new ArrayList<>(n);
        for (int i = 0; i < n; i++) {
            String[] parts = br.readLine().trim().split("\\s+");
            raw.add(new int[]{Integer.parseInt(parts[0]),
                              Integer.parseInt(parts[1]),
                              Integer.parseInt(parts[2])});
        }
        List<Integer> ans = solve(raw);
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < ans.size(); i++) {
            if (i > 0) sb.append(' ');
            sb.append(ans.get(i));
        }
        System.out.println(sb);
    }
}
```

**Why bottom‑up?**

* Recursion with memoization could also work, but a simple iterative DP eliminates any risk of stack overflow and is very straightforward to understand.

---

### 3.4  Python 3 (alternative short‑form)

```python
def solve():
    import sys
    data = sys.stdin.read().strip().split()
    if not data: 
        return
    it = iter(data)
    n = int(next(it))
    raw = [(int(next(it)), int(next(it)), int(next(it))) for _ in range(n)]
    raw.sort(key=lambda x: x[0])                 # sort by left endpoint

    starts = [x[0] for x in raw]
    def upper_bound(a, key):
        l, r = 0, len(a)
        while l < r:
            m = (l + r) >> 1
            if a[m] <= key: l = m + 1
            else: r = m
        return l

    next_idx = [upper_bound(starts, x[1]) for x in raw]
    dp_score = [[0]*5 for _ in range(n+1)]
    dp_list = [[[]]*5 for _ in range(n+1)]
    for i in range(n-1, -1, -1):
        for k in range(5):
            skip_score = dp_score[i+1][k]
            skip_list = dp_list[i+1][k]
            if k==0:
                dp_score[i][k] = skip_score
                dp_list[i][k] = skip_list
                continue
            take_score = raw[i][2] + dp_score[next_idx[i]][k-1]
            take_list = sorted(dp_list[next_idx[i]][k-1] + [i])
            if take_score > skip_score:
                dp_score[i][k] = take_score; dp_list[i][k] = take_list
            elif take_score < skip_score:
                dp_score[i][k] = skip_score; dp_list[i][k] = skip_list
            else:  # equal weight
                dp_score[i][k] = take_score
                dp_list[i][k] = min(skip_list, take_list)
    print(' '.join(map(str, dp_list[0][4])))
```

---

### 3.5  C++ 17

```cpp
#include <bits/stdc++.h>
using namespace std;

struct Interval {
    int l, r, w, idx;
};

int upperBound(const vector<int>& a, int key) {
    int l = 0, r = (int)a.size();
    while (l < r) {
        int m = (l + r) >> 1;
        if (a[m] <= key) l = m + 1;
        else r = m;
    }
    return l;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int n;
    if (!(cin >> n)) return 0;
    vector<Interval> v;
    for (int i = 0; i < n; ++i) {
        int l, r, w; cin >> l >> r >> w;
        v.emplace_back(l, r, w, i);
    }
    sort(v.begin(), v.end(), [](const Interval& a, const Interval& b){ return a.l < b.l; });

    vector<int> starts(n);
    for (int i = 0; i < n; ++i) starts[i] = v[i].l;
    vector<int> nxt(n);
    for (int i = 0; i < n; ++i) nxt[i] = upperBound(starts, v[i].r);

    vector<array<long long,5>> score(n+1);
    vector<array<vector<int>,5>> lst(n+1);
    for (int k = 0; k <= 4; ++k) {
        score[n][k] = 0;
        lst[n][k] = {};
    }

    for (int i = n-1; i >= 0; --i) {
        for (int k = 0; k <= 4; ++k) {
            long long skipS = score[i+1][k];
            const vector<int>& skipL = lst[i+1][k];

            if (k == 0) {
                score[i][k] = skipS;
                lst[i][k] = skipL;
                continue;
            }

            long long takeS = v[i].w + score[nxt[i]][k-1];
            vector<int> takeL = lst[nxt[i]][k-1];
            takeL.push_back(v[i].idx);
            sort(takeL.begin(), takeL.end());

            if (takeS > skipS) {
                score[i][k] = takeS;
                lst[i][k] = takeL;
            } else if (takeS < skipS) {
                score[i][k] = skipS;
                lst[i][k] = skipL;
            } else {
                // equal – lexicographic min
                score[i][k] = takeS;
                // lexicographic min of two sorted vectors
                const vector<int>& a = skipL, &b = takeL;
                vector<int> res = a;
                bool less = false;
                for (size_t t = 0; t < min(a.size(), b.size()); ++t) {
                    if (a[t] != b[t]) {
                        less = a[t] < b[t];
                        break;
                    }
                }
                if (less) res = a;
                else if (a.size() > b.size()) res = b;
                lst[i][k] = res;
            }
        }
    }
    for (size_t i = 0; i < lst[0][4].size(); ++i) {
        if (i) cout << ' ';
        cout << lst[0][4][i];
    }
    cout << '\n';
}
```

**Highlights**

* Uses `vector<array<>>` for DP arrays (fixed size 5 for `k`).
* Lexicographic comparison is done manually with a small loop.

---

### 3.6  C# 11

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;

public class Program
{
    private record Interval(int L, int R, int W, int Id);

    private record Result(long Score, List<int> Indices);

    public static void Main()
    {
        var input = Console.ReadLine();
        if (input == null) return;
        int n = int.Parse(input);
        var raw = new List<int[]>();
        for (int i = 0; i < n; i++)
        {
            var parts = Console.ReadLine().Split(' ', StringSplitOptions.RemoveEmptyEntries);
            raw.Add(new int[] { int.Parse(parts[0]), int.Parse(parts[1]), int.Parse(parts[2]) });
        }

        var ans = Solve(raw);
        Console.WriteLine(string.Join(' ', ans));
    }

    private static List<int> Solve(List<int[]> raw)
    {
        int n = raw.Count;
        var items = new List<Interval>(n);
        for (int i = 0; i < n; i++)
            items.Add(new Interval(raw[i][0], raw[i][1], raw[i][2], i));

        items.Sort((a, b) => a.L.CompareTo(b.L));

        int[] starts = items.Select(x => x.L).ToArray();
        int[] next = new int[n];
        for (int i = 0; i < n; i++)
            next[i] = UpperBound(starts, items[i].R);

        long[][] bestScore = new long[n + 1][];
        List<int>[][] bestList = new List<int>[n + 1][];
        for (int k = 0; k <= 4; k++)
        {
            bestScore[n] = new long[5];
            bestList[n] = new List<int>[5];
            for (int kk = 0; kk <= 4; kk++)
            {
                bestScore[n][kk] = 0;
                bestList[n][kk] = new List<int>();
            }
        }

        for (int i = n - 1; i >= 0; i--)
        {
            for (int k = 0; k <= 4; k++)
            {
                long skipScore = bestScore[i + 1][k];
                List<int> skipList = bestList[i + 1][k];

                if (k == 0)
                {
                    bestScore[i][k] = skipScore;
                    bestList[i][k] = skipList;
                    continue;
                }

                long takeScore = items[i].W + bestScore[next[i]][k - 1];
                var takeList = new List<int>(bestList[next[i]][k - 1]) { items[i].Id };
                takeList.Sort();

                if (takeScore > skipScore)
                {
                    bestScore[i][k] = takeScore;
                    bestList[i][k] = takeList;
                }
                else if (takeScore < skipScore)
                {
                    bestScore[i][k] = skipScore;
                    bestList[i][k] = skipList;
                }
                else // equal weight
                {
                    bestScore[i][k] = takeScore;
                    // lexicographic min
                    bestList[i][k] = LexicographicMin(skipList, takeList);
                }
            }
        }
        return bestList[0][4];
    }

    private static int UpperBound(int[] a, int key)
    {
        int l = 0, r = a.Length;
        while (l < r)
        {
            int m = (l + r) / 2;
            if (a[m] <= key) l = m + 1;
            else r = m;
        }
        return l;
    }

    private static List<int> LexicographicMin(List<int> a, List<int> b)
    {
        int len = Math.Min(a.Count, b.Count);
        for (int i = 0; i < len; i++)
        {
            if (a[i] != b[i]) return a[i] < b[i] ? a : b;
        }
        return a.Count <= b.Count ? a : b;
    }
}
```

* C# 11 uses `record` for brevity and immutable data structures.

---

## 4.  Explanation of the Algorithm (Dynamic Programming)

1. **Sorting**  
   All intervals are sorted by the left endpoint \(L_i\).  
   This guarantees that if interval *j* starts later than *i* (\(L_j > L_i\)), then in the sorted order *j* will appear after *i*.  
   The property needed for the DP recurrence is:  
   *The rightmost interval that ends before the left endpoint of the current interval is the predecessor.*  
   Because intervals are sorted by *L*, that predecessor can be found with a binary search on the array of left endpoints.

2. **Dynamic Programming**  
   For every position *i* (interval *i*) and for every number *k* of intervals already chosen (\(0\le k\le 4\)) we store:
   * `dp_score[i][k]` – maximum total weight attainable considering only the intervals with indices ≥ i when *k* intervals have already been chosen.
   * `dp_list[i][k]` – the corresponding sorted list of indices.

   Recurrence for interval *i* (0‑based index):

   ```
   skip:   choose nothing from interval i
           score = dp_score[i+1][k]
           list  = dp_list[i+1][k]

   take:   choose interval i (must not overlap with any later chosen interval)
           let p = next_index[i]   // first interval starting after R_i
           score = weight[i] + dp_score[p][k-1]
           list  = sorted( dp_list[p][k-1] ∪ {i} )
   ```

   If the two scores differ, we keep the larger one.  
   If the scores are equal, we keep the *lexicographically smallest* index list – this is what the statement asks for.

3. **Answer**  
   After the DP table is built, the desired maximum weight is `dp_score[0][4]`.  
   The associated index list `dp_list[0][4]` contains the four selected interval indices in ascending order.

4. **Complexities**  
   *Sorting:* \(O(n\log n)\).  
   *DP loop:* For each interval we iterate over 5 values of *k* and perform at most a few vector operations → \(O(n)\).  
   *Memory:* 5 × (n + 1) 64‑bit numbers + 5 × (n + 1) lists – linear in *n*.

---

## 5.  Edge Cases

* **Multiple optimal sets** – our lexicographic rule guarantees a unique answer.  
* **Intervals that do not overlap** – the DP automatically selects the four with largest weights.  
* **Very large input** – all solutions use fast I/O and run in \(O(n\log n)\) time.

---

## 6.  Reference Implementation – Java (fully commented)

```java
import java.io.*;
import java.util.*;

public class Main {

    /* ----------- data structure for an interval ----------- */
    private static class Interval {
        int l, r, w, idx;
        Interval(int l, int r, int w, int idx) {
            this.l = l; this.r = r; this.w = w; this.idx = idx;
        }
    }

    /* ----------- main ------------------------------------- */
    public static void main(String[] args) throws IOException {
        FastScanner fs = new FastScanner(System.in);
        int n = fs.nextInt();
        Interval[] a = new Interval[n];
        for (int i = 0; i < n; i++) {
            int l = fs.nextInt();
            int r = fs.nextInt();
            int w = fs.nextInt();
            a[i] = new Interval(l, r, w, i);        // indices are 0‑based
        }

        /* ---------- sort by left endpoint ------------------- */
        Arrays.sort(a, Comparator.comparingInt(o -> o.l));

        /* ---------- compute 'next' array -------------------- */
        int[] lefts = new int[n];
        for (int i = 0; i < n; i++) lefts[i] = a[i].l;

        int[] next = new int[n];
        for (int i = 0; i < n; i++) {
            next[i] = upperBound(lefts, a[i].r);    // first interval with L > R_i
        }

        /* ---------- DP -------------------------------------- */
        long[][] dpScore = new long[n + 1][5];      // dpScore[i][k]
        int[][][] dpList = new int[n + 1][5][];     // dpList[i][k] -> int[] of indices

        for (int i = n; i >= 0; i--) {             // from n-1 down to 0
            for (int k = 0; k <= 4; k++) {
                if (i == n) {                       // base: beyond last interval
                    dpScore[i][k] = 0;
                    dpList[i][k] = new int[0];
                    continue;
                }

                /* ----- option: skip current interval ----- */
                long skipScore = dpScore[i + 1][k];
                int[] skipList = dpList[i + 1][k];

                /* ----- option: take current interval ----- */
                long takeScore = a[i].w;
                int[] takeList = null;
                if (k > 0) {                           // we can take only if we still need intervals
                    takeScore += dpScore[next[i]][k - 1];
                    int[] tmp = dpList[next[i]][k - 1];
                    takeList = Arrays.copyOf(tmp, tmp.length + 1);
                    takeList[takeList.length - 1] = a[i].idx;   // append current index
                    Arrays.sort(takeList);
                } else {
                    // cannot take when k==0 (no more intervals needed)
                    takeScore = Long.MIN_VALUE;
                    takeList = new int[0];
                }

                /* ----- choose better option (lexicographic tie) ----- */
                if (takeScore > skipScore) {
                    dpScore[i][k] = takeScore;
                    dpList[i][k] = takeList;
                } else if (takeScore < skipScore) {
                    dpScore[i][k] = skipScore;
                    dpList[i][k] = skipList;
                } else { // equal scores – choose lexicographically smallest list
                    dpScore[i][k] = takeScore;      // same value
                    dpList[i][k] = lexicographicMin(skipList, takeList);
                }
            }
        }

        /* ---------- output --------------------------------- */
        int[] answer = dpList[0][4];      // indices of chosen intervals
        StringBuilder out = new StringBuilder();
        for (int i = 0; i < answer.length; i++) {
            if (i > 0) out.append(' ');
            out.append(answer[i]);
        }
        System.out.println(out.toString());
    }

    /* ---------- binary search: first index with value > key -------- */
    private static int upperBound(int[] arr, int key) {
        int l = 0, r = arr.length;
        while (l < r) {
            int m = (l + r) >>> 1;
            if (arr[m] <= key) l = m + 1;
            else r = m;
        }
        return l;
    }

    /* ---------- lexicographically smallest of two sorted int arrays */
    private static int[] lexicographicMin(int[] a, int[] b) {
        int minLen = Math.min(a.length, b.length);
        for (int i = 0; i < minLen; i++) {
            if (a[i] != b[i]) return a[i] < b[i] ? a : b;
        }
        return a.length <= b.length ? a : b;
    }

    /* ---------- fast scanner for int input -------------------------- */
    private static class FastScanner {
        private final InputStream in;
        private final byte[] buffer = new byte[1 << 16];
        private int ptr = 0, len = 0;
        FastScanner(InputStream in) { this.in = in; }
        private int read() throws IOException {
            if (ptr >= len) {
                len = in.read(buffer);
                ptr = 0;
                if (len <= 0) return -1;
            }
            return buffer[ptr++];
        }
        int nextInt() throws IOException {
            int c, sign = 1, val = 0;
            do c = read(); while (c <= ' ' && c != -1);
            if (c == '-') { sign = -1; c = read(); }
            while (c > ' ') { val = val * 10 + (c - '0'); c = read(); }
            return val * sign;
        }
    }
}
```

*The program follows exactly the algorithm explained above and is compatible with Java 17.*  

---

### End of Tutorial.