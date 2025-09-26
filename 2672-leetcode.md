---
title: LeetCode 2672. Number of Adjacent Elements With the Same Color - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Outline**

For every query we only change the colour of one cell.  
After this change we only need to know if this cell now forms a *pair*  
with its left neighbour or its right neighbour.

The total number of pairs is the answer for that query.  
Let  

```
cnt – current number of adjacent equal‑colour pairs
col[i] – current colour of cell i  (0 = uncoloured)
```

For a query `(pos , newColour)`:

1. **Remove the old pairs contributed by `col[pos]`.**  
   If `col[pos]` was non‑zero and matched the left neighbour → `cnt--`.  
   If it matched the right neighbour → `cnt--`.

2. **Paint the cell.**  
   `col[pos] = newColour`.

3. **Add the new pairs created by `newColour`.**  
   If the left neighbour is non‑zero and equals `newColour` → `cnt++`.  
   If the right neighbour is non‑zero and equals `newColour` → `cnt++`.

Store `cnt` after each query.  
All operations for one query are O(1), so the whole algorithm is O(m) where `m` is the number of queries.

---

### Python implementation

```python
from typing import List

class Solution:
    def colorTheArray(self, n: int, queries: List[List[int]]) -> List[int]:
        colors = [0] * n                # current colours, 0 = uncoloured
        res = []                         # answers for each query
        pairs = 0                        # current number of equal‑colour pairs

        for pos, new_colour in queries:
            old_colour = colors[pos]

            # remove old pairs involving pos
            if old_colour != 0:
                if pos > 0 and colors[pos-1] == old_colour:
                    pairs -= 1
                if pos + 1 < n and colors[pos+1] == old_colour:
                    pairs -= 1

            # paint pos with the new colour
            colors[pos] = new_colour

            # add new pairs created by the new colour
            if pos > 0 and colors[pos-1] == new_colour:
                pairs += 1
            if pos + 1 < n and colors[pos+1] == new_colour:
                pairs += 1

            res.append(pairs)

        return res
```

### Java implementation (15‑line clean code)

```java
class Solution {
    public int[] colorTheArray(int n, int[][] queries) {
        int[] col = new int[n];
        int[] ans = new int[queries.length];
        int cnt = 0;

        for (int i = 0; i < queries.length; i++) {
            int pos = queries[i][0];
            int newCol = queries[i][1];

            if (col[pos] != 0) {
                if (pos > 0 && col[pos-1] == col[pos]) cnt--;
                if (pos + 1 < n && col[pos+1] == col[pos]) cnt--;
            }

            col[pos] = newCol;

            if (pos > 0 && col[pos-1] == newCol) cnt++;
            if (pos + 1 < n && col[pos+1] == newCol) cnt++;

            ans[i] = cnt;
        }
        return ans;
    }
}
```

### C++ implementation (compact 15‑line)

```cpp
class Solution {
public:
    vector<int> colorTheArray(int n, vector<vector<int>>& queries) {
        vector<int> col(n, 0), ans(queries.size());
        int cnt = 0;
        for (int i = 0; i < queries.size(); ++i) {
            int pos = queries[i][0], c = queries[i][1];
            if (col[pos] != 0) {
                if (pos > 0 && col[pos-1] == col[pos]) --cnt;
                if (pos + 1 < n && col[pos+1] == col[pos]) --cnt;
            }
            col[pos] = c;
            if (pos > 0 && col[pos-1] == c) ++cnt;
            if (pos + 1 < n && col[pos+1] == c) ++cnt;
            ans[i] = cnt;
        }
        return ans;
    }
};
```

---

### Complexity

| Metric | Value |
|--------|-------|
| Time   | **O(m)**, `m` = number of queries |
| Space  | **O(n)** for the colour array (plus O(m) for the result) |

This solution is optimal – each query is processed in constant time.