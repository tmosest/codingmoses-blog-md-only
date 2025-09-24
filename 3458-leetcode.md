---
title: LeetCode 3458. Select K Disjoint Special Substrings - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For a substring to be *valid* every character that occurs inside the substring must have **all** of its
occurrences inside the same substring.  
In other words:

```
for every character x in the substring
        first[x]   >= start_of_substring
        last[x]    <= end_of_substring
```

The whole string itself is never counted as a valid substring.



--------------------------------------------------------------------

#### 1.   Observations

* The start of a valid substring can only be the **first occurrence** of a character.  
  (If the start was somewhere else, the same character would appear inside the
  substring and its first occurrence would lie outside – violating the rule.)

* The end of a valid substring is the **last occurrence** of some character that
  lies inside it.  
  When we try to build a substring from a starting position `i`, we
  first look at the last occurrence of `s[i]`.  
  While expanding the current end to the right we may have to include
  additional characters whose last occurrence is beyond the current end.
  If a character inside the interval has a first occurrence *before* `i`,
  then this whole interval is impossible – we simply skip it.

* After we know all valid intervals `[l , r]`, the problem turns into
  selecting the maximum number of non‑overlapping intervals.
  This is a classic greedy problem: sort all intervals by `r`
  (earliest finish first) and repeatedly pick the next interval that starts
  after the finish of the last chosen one.



--------------------------------------------------------------------

#### 2.   Algorithm
```
if k == 0                     →  true

1. Scan the string once and store
      first[26]  – first index of each character
      last[26]   – last  index of each character

2. For every position i that is the first occurrence of its character
      (i == first[s[i]]) :

        end = last[s[i]]                    // candidate end
        // Expand the end to cover every character inside
        for pos = i … end:
              end = max(end , last[s[pos]])

        // Check validity – no character inside may start outside [i , end]
        ok = true
        for pos = i … end:
              if first[s[pos]] < i : ok = false; break
        if !ok          → continue
        if i==0 and end==n-1  → continue   // whole string is not allowed

        store interval [i , end]

3. Sort all stored intervals by their right endpoint.

4. Greedy selection of non‑overlapping intervals
        lastEnd = -1
        chosen  = 0
        for every interval [l , r] in sorted order
              if l > lastEnd
                     chosen++, lastEnd = r
        return (chosen >= k)
```

--------------------------------------------------------------------

#### 3.   Correctness Proof  

We prove that the algorithm returns `true`
iff the string contains at least `k` non‑overlapping valid substrings.

---

##### Lemma 1  
For every character `c` all its occurrences lie in one of the intervals
constructed by the algorithm.

**Proof.**

During step 1 we compute `first[c]` and `last[c]`.
When we encounter a position `i` that equals `first[c]`,
the algorithm expands the candidate end until it contains every
character whose last occurrence is inside the current interval.
Thus the final interval `[i , end]` contains all occurrences of every
character appearing inside it.
In particular it contains all occurrences of `c`.  
Therefore the whole range `[first[c] , last[c]]`
is fully covered by a constructed interval (possibly together with other
characters). ∎



##### Lemma 2  
Every interval produced by the algorithm is a valid substring
according to the problem definition.

**Proof.**

Take any produced interval `[i , end]`.  
During its construction we performed two checks:

1. *Expansion* – after the first loop we enlarged `end` until every
   character inside `[i , end]` has its last occurrence ≤ `end`.  
   Thus for each character `x` inside the interval  
   `first[x] ≥ i` and `last[x] ≤ end`.

2. *Validity* – in the second loop we verified that no character
   inside has a first occurrence `< i`.  
   Combined with the previous property, for every character `x`
   in the substring  
   `first[x]` and `last[x]` lie inside `[i , end]`.

Hence the substring satisfies the definition of a valid substring.
∎



##### Lemma 3  
The greedy procedure in step 4 selects the maximum possible number of
non‑overlapping intervals among all constructed intervals.

**Proof.**

Intervals are sorted by finishing time.
The standard proof of the interval scheduling greedy algorithm
shows that for a set of intervals sorted by finishing time,
choosing the earliest finishing interval that does not overlap with the
previously chosen one yields an optimal solution.
The algorithm in step 4 is exactly that greedy algorithm. ∎



##### Lemma 4  
If the algorithm returns `true`, the string contains at least `k`
non‑overlapping valid substrings.

**Proof.**

`true` is returned only when the greedy selection in step 4 chose at least
`k` intervals.
By Lemma 2 each chosen interval is a valid substring, and by
Lemma 3 these intervals are pairwise non‑overlapping.
Therefore at least `k` valid, non‑overlapping substrings exist. ∎



##### Lemma 5  
If the string contains at least `k` non‑overlapping valid substrings,
the algorithm returns `true`.

**Proof.**

Let the `k` substrings be `T₁ , T₂ , … , T_k`
and order them by their start positions.
By Lemma 1 each occurrence of every character lies in one of the
intervals produced by the algorithm, therefore each `T_i` appears
in the list of intervals.
By Lemma 2 every listed interval is a valid substring.
Since the `T_i` are non‑overlapping,
the set of intervals that contains them is a feasible schedule.
By Lemma 3 the greedy algorithm will select **at least** as many
intervals as any feasible schedule, therefore it will select at least `k`
intervals and the algorithm returns `true`. ∎



##### Theorem  
The algorithm returns `true`  
iff the input string contains at least `k` non‑overlapping valid
substrings.

**Proof.**

*If part*:  
`true` ⇒ by Lemma 4 the greedy selection chose at least `k`
non‑overlapping intervals, each of them is a valid substring
(Lemma 2).  
Thus the string contains at least `k` such substrings.

*Only‑if part*:  
Assume the string contains at least `k` non‑overlapping valid substrings.
All of them are among the constructed intervals
(Lemma 1 and Lemma 2).
The greedy algorithm (Lemma 3) finds the maximum number of
non‑overlapping intervals among them,
which is therefore ≥ `k`.  
Consequently the algorithm returns `true`. ∎



--------------------------------------------------------------------

#### 4.   Complexity Analysis

```
Step 1   :  O(n)
Step 2   :  each position is visited at most once when expanding the end
           →  O(n)   (total across all starts)
Step 3   :  sort O(m log m)  (m – number of intervals, m ≤ n)
Step 4   :  O(m)
```

`m` never exceeds `n`, so the overall running time is **O(n)**  
and the memory consumption is **O(n)**.



--------------------------------------------------------------------

#### 5.   Reference Implementation (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    // Return true if at least k non‑overlapping valid substrings exist
    bool maxSubstringLength(string s, int k) {
        if (k <= 0) return true;          // edge case

        int n = (int)s.size();
        vector<int> first(26, n), last(26, -1);

        /* ---------- 1. first / last positions ---------- */
        for (int i = 0; i < n; ++i) {
            int c = s[i] - 'a';
            if (first[c] == n) first[c] = i;
            last[c] = i;
        }

        /* ---------- 2. build all valid intervals ---------- */
        vector<pair<int,int>> intervals;   // [l , r]
        for (int i = 0; i < n; ++i) {
            int ch = s[i] - 'a';
            if (first[ch] != i) continue;           // only start at first occ.

            int end = last[ch];                     // candidate end
            for (int pos = i; pos <= end; ++pos)
                end = max(end, last[s[pos] - 'a']); // expand to cover all

            // check validity – no char inside starts before i
            bool ok = true;
            for (int pos = i; pos <= end; ++pos) {
                int c = s[pos] - 'a';
                if (first[c] < i) { ok = false; break; }
            }
            if (!ok) continue;
            if (i == 0 && end == n - 1) continue;    // whole string is not allowed

            intervals.emplace_back(i, end);
        }

        /* ---------- 3. sort by finishing time ---------- */
        sort(intervals.begin(), intervals.end(),
             [](const auto& a, const auto& b){ return a.second < b.second; });

        /* ---------- 4. greedy interval scheduling ---------- */
        int chosen = 0;
        int lastEnd = -1;
        for (auto &iv : intervals) {
            if (iv.first > lastEnd) {
                ++chosen;
                lastEnd = iv.second;
                if (chosen >= k) return true;        // early exit
            }
        }
        return false;
    }
};
```

The code follows exactly the algorithm proven correct above and
conforms to the C++17 standard.