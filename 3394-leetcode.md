---
title: LeetCode 3394. Check if Grid can be Cut into Sections - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 3394. Check if Grid can be Cut into Sections  
**Medium** – LeetCode  
`public boolean checkValidCuts(int n, int[][] rectangles)`

---

### TL;DR  
* Sort the rectangles by their *y* (or *x*) borders.  
* Build two prefix‑/suffix‑arrays that tell you, for every possible cut line, how many rectangles lie completely below/above that line.  
* A pair of horizontal cuts is valid iff:  
  * at least one rectangle lies below the first cut,  
  * at least one rectangle lies above the second cut, and  
  * the remaining rectangles are sandwiched between the two cuts.  
* Do the same for vertical cuts.  
* Time complexity: **O(m log m)**, memory: **O(m)**, where *m* = 2 × number of rectangles.  
* Works for the worst‑case limits: *n* ≤ 10⁹, up to 10⁵ rectangles.

---

## The Brute‑Force Nightmare (The **Ugly**)

A naïve way is to enumerate **every** pair of cut coordinates:

```text
for each y1 in [0..n]
  for each y2 in (y1..n]
     if all rectangles lie entirely in one of the 3 horizontal stripes
        return true
```

But `n` can be 10⁹! Even if we only look at the *y* values that actually appear in the input (there are at most 2·10⁵ such values), checking a single pair still requires scanning all 10⁵ rectangles.  
That would be ~10¹⁰ operations – far beyond what any interview or production system can handle.

---

## The Elegant Sweep (The **Good**)

The problem has a hidden structure:  

*Rectangles do not overlap.*  
Therefore the **projection** of every rectangle onto the *y*‑axis is a disjoint interval `[minY, maxY]`.  
The same holds for the *x*‑axis.

If we treat every distinct border value (both starts and ends) as a potential cut line, we can answer the question “how many rectangles lie below *this* line?” in *O(1)* time after a one‑time preprocessing step.  
The trick is to build a **prefix‑sum** over the *end* borders and a **suffix‑sum** over the *start* borders.

Once we have those two helpers, a horizontal pair of cuts is found in a single pass over the possible cut positions – no nested loops, no per‑rectangle checks.

---

## The Algorithm, Step‑by‑Step

1. **Collect all borders**  
   * For every rectangle push its `startY` and `endY` into an array.  
   * Sort the array and remove duplicates → `yVals` (size *m*).

2. **Map a border value → index**  
   * Use a `Map<int, int>` (or a dictionary in Python) so we can turn a coordinate into an array index in *O(log m)*.

3. **Count rectangles that end / start at every index**  
   ```text
   endCount[i]   = how many rectangles have endY == yVals[i]
   startCount[i] = how many rectangles have startY == yVals[i]
   ```

4. **Prefix / suffix**  
   ```text
   below[i] = Σ endCount[0..i]          // rectangles completely below cut y = yVals[i]
   above[i] = Σ startCount[i..m‑1]      // rectangles completely above cut y = yVals[i]
   ```

5. **Try all first cuts**  
   ```text
   for k = 0 .. m‑2
       bottom = below[k]
       if bottom == 0: continue

       // find the earliest second cut that has at least one rectangle above it
       l = k + 1
       while l < m and above[l] == 0: l++

       if l >= m: break

       top    = above[l]
       middle = totalRectangles - bottom - top
       if middle > 0: return true
   ```

   *Why is a single search enough?*  
   As we move the second cut to the right, `above` never increases – it only stays the same or decreases.  
   Therefore the number of rectangles in the top stripe can only shrink, while the middle stripe can only shrink as well.  
   If the middle stripe is empty at the first viable `l`, it will stay empty for all larger `l`. So a single check per `k` suffices.

6. **Do the same for the *x*‑axis** – exactly the same code, just swapped `x` ↔︎ `y`.

7. **Return** `true` if either orientation works, otherwise `false`.

---

## Full Reference Implementation

Below are three idiomatic solutions – **Java**, **Python** and **C++** – each annotated so you can copy‑paste them into an interview room or a LeetCode test harness.

---

### 1. Java

```java
import java.util.*;

public class Solution {
    public boolean checkValidCuts(int n, int[][] rectangles) {
        // ----- horizontal cuts -----
        if (validCuts(n, rectangles, true)) return true;
        // ----- vertical cuts -----
        if (validCuts(n, rectangles, false)) return true;
        return false;
    }

    // direction = true → work on y, false → work on x
    private boolean validCuts(int n, int[][] rects, boolean horizontal) {
        int m = rects.length;
        int[] coords = new int[2 * m];
        int ptr = 0;
        for (int[] r : rects) {
            int a = horizontal ? r[1] : r[0]; // start
            int b = horizontal ? r[3] : r[2]; // end
            coords[ptr++] = a;
            coords[ptr++] = b;
        }

        // unique, sorted
        Arrays.sort(coords);
        int[] uniq = new int[ptr];
        int sz = 0;
        for (int i = 0; i < ptr; i++) {
            if (i == 0 || coords[i] != coords[i - 1]) {
                uniq[sz++] = coords[i];
            }
        }
        uniq = Arrays.copyOf(uniq, sz);        // real length

        // map coordinate → index
        Map<Integer, Integer> idx = new HashMap<>(sz * 2);
        for (int i = 0; i < sz; i++) idx.put(uniq[i], i);

        int[] startCnt = new int[sz];
        int[] endCnt   = new int[sz];
        for (int[] r : rects) {
            int start = horizontal ? r[1] : r[0];
            int end   = horizontal ? r[3] : r[2];
            startCnt[idx.get(start)]++;
            endCnt[idx.get(end)]++;
        }

        // prefix of endCnt = number of rectangles with end <= cut
        int[] below = new int[sz];
        int cum = 0;
        for (int i = 0; i < sz; i++) {
            cum += endCnt[i];
            below[i] = cum;
        }

        // suffix of startCnt = number of rectangles with start >= cut
        int[] above = new int[sz];
        cum = 0;
        for (int i = sz - 1; i >= 0; i--) {
            cum += startCnt[i];
            above[i] = cum;
        }

        // try all first cut positions
        for (int k = 0; k < sz - 1; k++) {
            int bottom = below[k];
            if (bottom == 0) continue;            // need at least one below

            int l = k + 1;
            while (l < sz && above[l] == 0) l++;   // skip empty top sections
            if (l >= sz) break;

            int top    = above[l];
            int middle = m - bottom - top;         // total = m rectangles

            if (middle > 0 && top > 0) return true;
        }
        return false;
    }
}
```

> **Why it works**  
> * `below[k]` counts all rectangles whose **end** is ≤ the candidate first cut.  
> * `above[l]` counts all rectangles whose **start** is ≥ the second cut.  
> * `m - bottom - top` is exactly the number of rectangles that sit strictly between the two cuts.  
> If every part is non‑empty we have a valid pair of cuts.

---

### 2. Python

```python
def checkValidCuts(n: int, rectangles: List[List[int]]) -> bool:
    def try_cuts(coord_start, coord_end):
        """Same logic as Java, but written for Python."""
        m = len(rectangles)

        # collect all border values
        coords = []
        for a, b, c, d in rectangles:
            coords.append(coord_start)
            coords.append(coord_end)

        # unique sorted
        uniq = sorted(set(coords))
        idx = {v: i for i, v in enumerate(uniq)}

        start_cnt = [0] * len(uniq)
        end_cnt   = [0] * len(uniq)

        for a, b, c, d in rectangles:
            start_cnt[idx[a]] += 1
            end_cnt[idx[d]]   += 1

        # prefix of end_cnt
        below = [0] * len(uniq)
        s = 0
        for i, val in enumerate(uniq):
            s += end_cnt[i]
            below[i] = s

        # suffix of start_cnt
        above = [0] * len(uniq)
        s = 0
        for i in range(len(uniq) - 1, -1, -1):
            s += start_cnt[i]
            above[i] = s

        total = m
        for k in range(len(uniq) - 1):
            bottom = below[k]
            if bottom == 0:
                continue
            l = k + 1
            while l < len(uniq) and above[l] == 0:
                l += 1
            if l >= len(uniq):
                break
            top = above[l]
            middle = total - bottom - top
            if middle > 0:
                return True
        return False

    # horizontal cuts (y‑axis)
    if try_cuts(lambda r: r[1], lambda r: r[3]):
        return True
    # vertical cuts (x‑axis)
    if try_cuts(lambda r: r[0], lambda r: r[2]):
        return True
    return False
```

> *Python* keeps the same O(m log m) behaviour while being very terse.

---

### 3. C++

```cpp
class Solution {
public:
    bool checkValidCuts(int n, vector<vector<int>>& rects) {
        if (solve(rects, true))  return true;   // horizontal
        if (solve(rects, false)) return true;   // vertical
        return false;
    }

private:
    bool solve(vector<vector<int>>& rects, bool horizontal) {
        int m = rects.size();

        // 1. collect borders
        vector<int> coords;
        coords.reserve(2 * m);
        for (auto &r : rects) {
            int start = horizontal ? r[1] : r[0];
            int end   = horizontal ? r[3] : r[2];
            coords.push_back(start);
            coords.push_back(end);
        }

        // 2. unique sorted
        sort(coords.begin(), coords.end());
        coords.erase(unique(coords.begin(), coords.end()), coords.end());

        // 3. coordinate → index map
        unordered_map<int, int> idx;
        idx.reserve(coords.size() * 2);
        for (int i = 0; i < (int)coords.size(); ++i) idx[coords[i]] = i;

        vector<int> startCnt(coords.size(), 0), endCnt(coords.size(), 0);
        for (auto &r : rects) {
            int start = horizontal ? r[1] : r[0];
            int end   = horizontal ? r[3] : r[2];
            ++startCnt[idx[start]];
            ++endCnt[idx[end]];
        }

        // 4. prefix of endCnt → below
        vector<int> below(coords.size(), 0);
        int cur = 0;
        for (int i = 0; i < (int)coords.size(); ++i) {
            cur += endCnt[i];
            below[i] = cur;
        }

        // 5. suffix of startCnt → above
        vector<int> above(coords.size(), 0);
        cur = 0;
        for (int i = (int)coords.size() - 1; i >= 0; --i) {
            cur += startCnt[i];
            above[i] = cur;
        }

        // 6. try all first cuts
        for (int k = 0; k < (int)coords.size() - 1; ++k) {
            int bottom = below[k];
            if (!bottom) continue;           // need something below

            int l = k + 1;
            while (l < (int)coords.size() && !above[l]) ++l;
            if (l >= (int)coords.size()) break;

            int top = above[l];
            int middle = m - bottom - top;
            if (middle > 0) return true;
        }
        return false;
    }
};
```

> The C++ version follows the same pipeline, but leverages `unordered_map` and `vector<int>` for speed.

---

## Complexity Analysis

| Step | Time | Space |
|------|------|-------|
| Border collection | **O(m)** | **O(2 m)** |
| Sorting + dedup | **O(m log m)** | **O(m)** |
| Building counts | **O(m)** | **O(m)** |
| Checking all first cuts | **O(m)** | **O(1)** per `k` |
| **Total** | **O(m log m)** | **O(m)** |

Memory usage is linear in the number of distinct borders (≤ 2 m).

---

## What If You Want to Handle the Full [0, n] Range?

The above algorithm only uses the borders that actually appear in the input.  
If you also want to allow a cut at `0` or at `n` (even if no rectangle touches them), simply add those values to the border list before deduplication.  
Everything else stays identical.

---

## Edge Cases & Common Pitfalls

| Case | Why it matters | How the code handles it |
|------|----------------|------------------------|
| **Only one rectangle** | Impossible to split → always `false` | `below[0]` will be 0 for any first cut; loop will never return true. |
| **All rectangles stacked** (touching each other) | Cuts can only go through empty space | The algorithm will correctly return `false`. |
| **Rectangles sharing a border** | `start == end` not allowed by the problem, but if it occurs it won’t break the algorithm | Since the interval `[minY, maxY]` would be empty, such rectangles are ignored automatically. |
| **Large coordinates** | Need 64‑bit type if `n` is > 2 B | Use `long long` or 64‑bit ints where appropriate. |

---

## Final Thoughts

- The **core idea** – prefix of ends + suffix of starts – is universal for interval‑based problems.  
- Once you have those helpers, *any* question that asks “how many intervals lie entirely on one side of a threshold” can be answered in *O(1)*.  
- The algorithm runs in *O(m log m)* time and *O(m)* space, comfortably fast for up to `10^5` rectangles, which is typical for LeetCode constraints.

Feel free to adapt the code to your language of choice, tweak the comments, and bring the intuition of “disjoint borders → two prefix arrays” to your next interview. Good luck! 🚀

---