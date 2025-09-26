---
title: LeetCode 2055. Plates Between Candles - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Idea –  O(n + q)**  

For every query `(l , r)` we need  

* the first `|` at or after `l`  – call it **L**  
* the last  `|` at or before `r` – call it **R**  
* the number of `*` between **L** and **R**.

If such a pair of pipes does not exist (there is no `|` on the left of `r` or no `|` on the right of `l`) the answer is `0`.

All the information we need can be obtained in a single scan of the string:

| variable | description | construction |
|----------|-------------|--------------|
| `nextPipe[i]` | distance from `i` to the **nearest `|` on the right** (including itself).  `-1` if none. | scan from right to left. |
| `prevPipe[i]` | distance from `i` to the **nearest `|` on the left** (including itself).  `-1` if none. | scan from left to right. |
| `starPref[i]` | number of `*` **strictly before** position `i` (i.e. in `[0 , i)`). | standard prefix sum. |

With these three arrays every query is answered in O(1).

---

#### Pre‑processing (O(n))

```cpp
int n = s.size();
vector<int> starPref(n + 1, 0);          // starPref[0] = 0
vector<int> nextPipe(n, -1);             // distance to next '|'
vector<int> prevPipe(n, -1);             // distance to previous '|'

int lastPipe = -1;          // distance to the closest '|' on the left
for (int i = 0; i < n; ++i) {
    if (s[i] == '*')
        starPref[i + 1] = starPref[i] + 1;
    else
        starPref[i + 1] = starPref[i];

    if (s[i] == '|') lastPipe = 0;   // current position is a pipe
    else if (lastPipe != -1) ++lastPipe;
    prevPipe[i] = lastPipe;          // distance to the nearest '|' on the left
}
```

```cpp
int nextPipeDist = -1;
for (int i = n - 1; i >= 0; --i) {
    if (s[i] == '|') nextPipeDist = 0;
    else if (nextPipeDist != -1) ++nextPipeDist;
    nextPipe[i] = nextPipeDist;      // distance to the nearest '|' on the right
}
```

Now

* index of the first pipe after position `l`  → `l + nextPipe[l]`  (if `nextPipe[l] == -1` → no pipe)
* index of the last  pipe before position `r` → `r - prevPipe[r]`   (if `prevPipe[r] == -1` → no pipe)

---

#### Answering a query (O(1))

```cpp
int left  = queries[i][0];
int right = queries[i][1];

int afterL  = (nextPipe[left]  == -1) ? -1 : left  + nextPipe[left];
int beforeR = (prevPipe[right] == -1) ? -1 : right - prevPipe[right];

if (afterL == -1 || beforeR == -1 || afterL > right || beforeR < left) {
    ans[i] = 0;                         // no valid pair of pipes
} else {
    ans[i] = starPref[beforeR] - starPref[afterL];
}
```

The `starPref` array is defined so that `starPref[x]` equals the number of `*` **strictly before** position `x`.  
Hence `starPref[beforeR] - starPref[afterL]` counts exactly the `*` between the two pipes.

---

#### Complexity

*Time* : `O(n + q)` – one linear pass for the preprocessing and one for the queries.  
*Space*: `O(n)` – three integer arrays of length `n`.

---

#### Full C++ implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<int> platesBetweenCandles(string s, vector<vector<int>>& queries) {
        int n = (int)s.size();

        /* ---------- preprocessing ---------- */
        vector<int> starPref(n + 1, 0);          // starPref[i] = # of '*' in [0, i)
        vector<int> nextPipe(n, -1);             // distance to next '|'
        vector<int> prevPipe(n, -1);             // distance to previous '|'

        int dist = -1;                           // distance to the closest '|' on the left
        for (int i = 0; i < n; ++i) {
            if (s[i] == '*')
                starPref[i + 1] = starPref[i] + 1;
            else
                starPref[i + 1] = starPref[i];

            if (s[i] == '|') dist = 0;
            else if (dist != -1) ++dist;
            prevPipe[i] = dist;                  // distance to nearest pipe on the left
        }

        dist = -1;                               // now from right to left
        for (int i = n - 1; i >= 0; --i) {
            if (s[i] == '|') dist = 0;
            else if (dist != -1) ++dist;
            nextPipe[i] = dist;                  // distance to nearest pipe on the right
        }

        /* ---------- answering queries ---------- */
        vector<int> ans(queries.size(), 0);
        for (int i = 0; i < (int)queries.size(); ++i) {
            int l = queries[i][0];
            int r = queries[i][1];

            int afterL  = (nextPipe[l]  == -1) ? -1 : l  + nextPipe[l];
            int beforeR = (prevPipe[r] == -1) ? -1 : r - prevPipe[r];

            if (afterL == -1 || beforeR == -1 || afterL > r || beforeR < l)
                ans[i] = 0;
            else
                ans[i] = starPref[beforeR] - starPref[afterL];
        }
        return ans;
    }
};
```

The code follows the algorithm described above and works in the required `O(n + q)` time and `O(n)` space.