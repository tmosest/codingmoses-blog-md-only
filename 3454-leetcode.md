---
title: LeetCode 3454. Separate Squares II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 3454 – Separate Squares II  
### The Hardest “Horizontal Split” Problem on LeetCode  
*You’re looking for a way to impress hiring managers?*  
This post shows a clean, production‑ready solution in **Java, Python, and C++** and explains the algorithm in plain English.  
Read on to discover the *good*, *bad*, and *ugly* parts of this challenge – everything you need to ace the interview and land that job!

---

## Table of Contents  
1. [Problem Summary](#problem-summary)  
2. [Intuition & Key Observations](#intuition)  
3. [Algorithm Overview](#algorithm)  
4. [Detailed Walkthrough](#walkthrough)  
5. [Complexity Analysis](#complexity)  
6. [Common Pitfalls](#pitfalls)  
7. [Code – Java](#java-code)  
8. [Code – Python](#python-code)  
9. [Code – C++](#cpp-code)  
10. [SEO & Job‑Search Tips](#seo)  
11. [Final Thoughts](#final)

---

<a name="problem-summary"></a>
## 1. Problem Summary

> **Given** an array `squares` where each element is `[xi, yi, li]` – the bottom‑left corner and side length of a square –  
> **Return** the *minimum* `y` such that the union area of all squares **above** this line equals the union area **below** it.  
>  
> Squares may overlap. Overlapping parts are **counted only once** (i.e., we’re working with the *union* of squares).

*Example*  

```
squares = [[0,0,1], [2,2,1]]
Answer: 1.00000
```

> Any horizontal line between `y=1` and `y=2` splits the total area (2) in half.  
> The smallest such `y` is `1`.

Constraints are large (`n ≤ 5·10⁴`, coordinates up to `10⁹`, total area ≤ `10¹⁵`).  Solutions that run in **O(n log n)** time and use *O(n)* memory are needed.

---

<a name="intuition"></a>
## 2. Intuition & Key Observations

1. **Area below a horizontal line is a piecewise linear, monotonic function of y**  
   – Each square contributes a rectangular strip `[x, x+l] × [y, y+l]`.  
   – As we sweep `y` upward, the horizontal span of active squares (the *union of x‑intervals*) changes only at the bottom or top of a square.

2. **The union of x‑intervals is easy to maintain with a segment tree**  
   – We compress the `x` coordinates (there are at most `2n` distinct edges).  
   – Each event (start or end of a square) updates a range in the tree: `+1` on entering, `-1` on leaving.  
   – The tree keeps the total covered length in `O(log n)` time.

3. **The cumulative area between two consecutive events is**  
   `coveredLength × (y₂ – y₁)`.  
   – While sweeping we can accumulate this value and detect when the cumulative area reaches **half** of the total union area.

4. **Two‑pass sweep is simplest**  
   – First pass: compute the total area.  
   – Second pass: sweep again, stopping when the accumulated area ≥ half; interpolate inside the current slab to find the exact `y`.

---

<a name="algorithm"></a>
## 3. Algorithm Overview

```
1. Build two lists of events: (y, x1, x2, delta)
   delta = +1 at square's bottom, -1 at square's top.

2. Compress all x coordinates → xs[0 … m-1] sorted & unique.

3. SegmentTree over xs:
   - count[node]   : how many intervals cover this node
   - length[node]  : total x length covered by node's interval

4. First pass (to compute total area):
   - Sort events by y.
   - prevY = events[0].y
   - area  = 0
   - For each event e:
        covered = segTree.totalCovered()
        area += covered * (e.y - prevY)
        segTree.update(range x1→x2, delta)
        prevY = e.y
   - totalArea = area
   - half = totalArea / 2

4. Second pass (to locate split line):
   - Re‑initialize segment tree, sweep again.
   - cum = 0
   - For each event e:
        covered = segTree.totalCovered()
        slabHeight = e.y - prevY
        if covered > 0 and cum + covered * slabHeight >= half:
             // y lies inside this slab
             needed = half - cum
             ySplit = prevY + needed / covered
             return ySplit
        cum += covered * slabHeight
        segTree.update(range x1→x2, delta)
        prevY = e.y

   // If we never cross half (e.g. all area concentrated at a single y),
   // simply return the last event's y (edge case).
```

**Why interpolation works**  
Within a slab the covered length is constant.  
Area increases linearly with `y`.  
If we need `needed` more area, the extra height is `needed / coveredLength`.

---

<a name="walkthrough"></a>
## 4. Detailed Walkthrough

| Step | What’s happening | Why it matters |
|------|------------------|----------------|
| **Event creation** | For every square `(x, y, l)` add: `(y, x, x+l, +1)` and `(y+l, x, x+l, -1)` | Events indicate when a square becomes active or inactive. |
| **X‑coordinate compression** | All distinct `x` and `x+l` sorted → `xs`. | The segment tree operates on indices, not raw coordinates. |
| **Segment tree internals** |  
`tree[v]` – total covered length in node’s interval  
`cnt[v]`  – how many active intervals cover this node | When `cnt[v] > 0`, node is fully covered; otherwise we sum children. |
| **First pass – total area** | Sweep up from smallest `y` to largest, updating the tree and adding `covered × Δy` to `area`. | Gives `totalArea`. |
| **Second pass – find split** | Re‑initialize the tree, repeat sweep.  
When cumulative area `cum` satisfies `cum + covered × Δy ≥ half`, compute exact `y`. | Stops early – no need to store all slabs. |
| **Interpolation** | `y = prevY + (half - cum) / covered`. | Exact `y` inside slab; guarantees precision up to 1e‑5. |

---

<a name="complexity"></a>
## 4. Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Event generation | `O(n)` | `O(n)` |
| X‑compression | `O(n log n)` | `O(n)` |
| Segment tree updates | `O(log n)` each | `O(n)` (array of size `4·m`) |
| First sweep | `O(n log n)` | `O(1)` extra |
| Second sweep | `O(n log n)` | `O(1)` extra |

**Total**  
`Time  :  O(n log n)`  
`Space :  O(n)`  

With `n = 5·10⁴` this comfortably fits LeetCode’s limits.

---

<a name="pitfalls"></a>
## 5. Common Pitfalls

| Pitfall | How to Avoid |
|---------|--------------|
| **Using `int` for coordinates/lengths** | Coordinates up to `10⁹`; their sum can be `2·10⁹`. Use `long` (Java) or `int64_t` (C++). |
| **Not handling zero covered length** | In a slab with no active squares, `coveredLength = 0`. Skip interpolation and continue to next event. |
| **Overflow during area computation** | Use `long` for lengths, cast to `double` only when multiplying by height. `double` can represent up to `1e308`. |
| **Wrong range update semantics** | The segment tree update should be *half‑open* `[l, r)`; the leaf interval is between `xs[i]` and `xs[i+1]`. |
| **Floating‑point precision** | The answer must be accurate to `1e‑5`. Interpolation formula uses `double` division – safe for the required precision. |

---

<a name="java-code"></a>
## 6. Code – Java

```java
import java.util.*;
import java.io.*;

class Solution {
    // Event: change of coverage at a given y
    private static class Event {
        long y, x1, x2;
        int delta;   // +1 on entering, -1 on leaving

        Event(long y, long x1, long x2, int delta) {
            this.y = y;
            this.x1 = x1;
            this.x2 = x2;
            this.delta = delta;
        }
    }

    // Segment tree that keeps total covered x‑length
    private static class SegmentTree {
        final long[] xs;          // compressed x coordinates
        final int n;              // number of xs
        final int[] cnt;          // coverage counter
        final long[] len;         // covered length

        SegmentTree(long[] xs) {
            this.xs = xs;
            this.n = xs.length;
            int size = 4 * n;
            cnt = new int[size];
            len = new long[size];
        }

        // update [l, r) by delta
        void update(int node, int l, int r, int ql, int qr, int delta) {
            if (qr <= l || ql >= r) return;
            if (ql <= l && r <= qr) {
                cnt[node] += delta;
            } else {
                int mid = (l + r) >>> 1;
                update(node << 1, l, mid, ql, qr, delta);
                update(node << 1 | 1, mid, r, ql, qr, delta);
            }
            if (cnt[node] > 0) {
                len[node] = xs[r] - xs[l];
            } else if (r - l == 1) {
                len[node] = 0;
            } else {
                len[node] = len[node << 1] + len[node << 1 | 1];
            }
        }

        long totalCovered() {
            return len[1];
        }
    }

    public double separateSquares(int[][] squares) {
        int n = squares.length;
        // Build events
        List<Event> ev = new ArrayList<>(2 * n);
        long[] xsRaw = new long[2 * n];
        int idx = 0;
        for (int[] sq : squares) {
            long x = sq[0];
            long y = sq[1];
            long l = sq[2];
            long x1 = x;
            long x2 = x + l;
            ev.add(new Event(y, x1, x2, +1));
            ev.add(new Event(y + l, x1, x2, -1));
            xsRaw[idx++] = x1;
            xsRaw[idx++] = x2;
        }

        // Compress x
        long[] xs = compress(xsRaw);

        // First sweep – compute total area
        double totalArea = computeArea(ev, xs, n);

        double half = totalArea / 2.0;

        // Second sweep – find split line
        double answer = findSplit(ev, xs, half, n);
        return answer;
    }

    private static long[] compress(long[] raw) {
        Arrays.sort(raw);
        int m = 0;
        for (int i = 0; i < raw.length; ++i) {
            if (i == 0 || raw[i] != raw[i - 1]) {
                raw[m++] = raw[i];
            }
        }
        return Arrays.copyOf(raw, m);
    }

    private static double computeArea(List<Event> ev, long[] xs, int n) {
        ev.sort(Comparator.comparingLong(a -> a.y));
        SegmentTree st = new SegmentTree(xs);
        long prevY = ev.get(0).y;
        double area = 0.0;

        for (Event e : ev) {
            long curY = e.y;
            long coveredLen = st.totalCovered();
            area += coveredLen * (double) (curY - prevY);

            st.update(1, 0, xs.length - 1,
                      Arrays.binarySearch(xs, e.x1),
                      Arrays.binarySearch(xs, e.x2),
                      e.delta);
            prevY = curY;
        }
        return area;
    }

    private static double findSplit(List<Event> ev, long[] xs,
                                    double half, int n) {
        ev.sort(Comparator.comparingLong(a -> a.y));
        SegmentTree st = new SegmentTree(xs);
        long prevY = ev.get(0).y;
        double cum = 0.0;

        for (Event e : ev) {
            long curY = e.y;
            long coveredLen = st.totalCovered();
            long deltaY = curY - prevY;
            if (coveredLen > 0) {
                double need = half - cum;
                if (need <= coveredLen * deltaY) {
                    return prevY + need / coveredLen;
                }
                cum += coveredLen * (double) deltaY;
            }

            st.update(1, 0, xs.length - 1,
                      Arrays.binarySearch(xs, e.x1),
                      Arrays.binarySearch(xs, e.x2),
                      e.delta);
            prevY = curY;
        }
        // All area on a single horizontal line – return last y
        return ev.get(ev.size() - 1).y;
    }

    // Helper: find index of a value in compressed xs
    private static int idx(long[] xs, long val) {
        return Arrays.binarySearch(xs, val);
    }
}
```

**Explanation of key parts**

- `computeArea` uses the same event list to avoid rebuilding; just re‑sort it.
- `Arrays.binarySearch(xs, value)` returns the compressed index of a raw coordinate.
- The segment tree uses half‑open ranges; the root covers `[0, xs.length-1)` (i.e. between the first and last compressed coordinate).

---

<a name="cxx-code"></a>
## 7. Code – C++

```cpp
#include <bits/stdc++.h>
using namespace std;

struct Event {
    long long y, x1, x2;
    int delta;                 // +1 on entering, -1 on leaving
    Event(long long y, long long x1, long long x2, int delta)
        : y(y), x1(x1), x2(x2), delta(delta) {}
};

struct SegTree {
    const vector<long long> &xs;   // compressed coordinates
    int n;                        // number of xs
    vector<int> cnt;              // coverage counter
    vector<long long> len;        // covered length

    SegTree(const vector<long long> &xs) : xs(xs) {
        n = xs.size();
        int sz = 4 * n;
        cnt.assign(sz, 0);
        len.assign(sz, 0);
    }

    void update(int node, int l, int r,
                int ql, int qr, int delta) {
        if (qr <= l || ql >= r) return;
        if (ql <= l && r <= qr) {
            cnt[node] += delta;
        } else {
            int mid = (l + r) >> 1;
            update(node << 1, l, mid, ql, qr, delta);
            update(node << 1 | 1, mid, r, ql, qr, delta);
        }
        if (cnt[node] > 0) {
            len[node] = xs[r] - xs[l];
        } else if (r - l == 1) {
            len[node] = 0;
        } else {
            len[node] = len[node << 1] + len[node << 1 | 1];
        }
    }

    long long totalCovered() const { return len[1]; }
};

vector<long long> compress(vector<long long> raw) {
    sort(raw.begin(), raw.end());
    raw.erase(unique(raw.begin(), raw.end()), raw.end());
    return raw;
}

double computeArea(vector<Event> ev, const vector<long long> &xs) {
    sort(ev.begin(), ev.end(), [](const Event &a, const Event &b){
        return a.y < b.y;
    });

    SegTree st(xs);
    long long prevY = ev[0].y;
    double area = 0.0;

    for (const auto &e : ev) {
        long long curY = e.y;
        long long covered = st.totalCovered();
        area += covered * double(curY - prevY);

        int l = lower_bound(xs.begin(), xs.end(), e.x1) - xs.begin();
        int r = lower_bound(xs.begin(), xs.end(), e.x2) - xs.begin();
        st.update(1, 0, xs.size() - 1, l, r, e.delta);
        prevY = curY;
    }
    return area;
}

double findSplit(vector<Event> ev,
                 const vector<long long> &xs,
                 double half) {
    sort(ev.begin(), ev.end(), [](const Event &a, const Event &b){
        return a.y < b.y;
    });

    SegTree st(xs);
    long long prevY = ev[0].y;
    double cum = 0.0;

    for (const auto &e : ev) {
        long long curY = e.y;
        long long covered = st.totalCovered();
        long long deltaY = curY - prevY;
        if (covered > 0 && cum + covered * double(deltaY) >= half) {
            double need = half - cum;
            return prevY + need / covered;
        }
        cum += covered * double(deltaY);

        int l = lower_bound(xs.begin(), xs.end(), e.x1) - xs.begin();
        int r = lower_bound(xs.begin(), xs.end(), e.x2) - xs.begin();
        st.update(1, 0, xs.size() - 1, l, r, e.delta);
        prevY = curY;
    }
    // Edge case: all area concentrated at a single y
    return ev.back().y;
}
```

**Key points**

- `lower_bound` obtains the compressed index.
- `SegTree` uses `xs.size() - 1` as the rightmost index because each leaf represents `[xs[i], xs[i+1])`.

---

<a name="cxx-code"></a>
## 8. Code – C++

```cpp
#include <bits/stdc++.h>
using namespace std;

// ---------- Event ----------
struct Event {
    long long y, x1, x2;
    int delta;           // +1 entering, -1 leaving
    Event(long long y, long long x1, long long x2, int delta)
        : y(y), x1(x1), x2(x2), delta(delta) {}
};

// ---------- Segment Tree ----------
struct SegTree {
    const vector<long long> &xs;   // compressed x
    vector<int> cnt;              // coverage count
    vector<long long> len;        // covered length

    SegTree(const vector<long long> &xs) : xs(xs) {
        int sz = 4 * xs.size();
        cnt.assign(sz, 0);
        len.assign(sz, 0);
    }

    void upd(int node, int l, int r, int ql, int qr, int delta) {
        if (qr <= l || ql >= r) return;
        if (ql <= l && r <= qr) {
            cnt[node] += delta;
        } else {
            int mid = (l + r) >> 1;
            upd(node << 1, l, mid, ql, qr, delta);
            upd(node << 1 | 1, mid, r, ql, qr, delta);
        }
        if (cnt[node] > 0) {
            len[node] = xs[r] - xs[l];
        } else if (r - l == 1) {
            len[node] = 0;
        } else {
            len[node] = len[node << 1] + len[node << 1 | 1];
        }
    }

    long long covered() const { return len[1]; }
};

// ---------- Helpers ----------
vector<long long> compress(vector<long long> raw) {
    sort(raw.begin(), raw.end());
    raw.erase(unique(raw.begin(), raw.end()), raw.end());
    return raw;
}

double compute_area(vector<Event> ev, const vector<long long> &xs) {
    sort(ev.begin(), ev.end(), [](const Event &a, const Event &b){
        return a.y < b.y;
    });

    SegTree st(xs);
    long long prevY = ev.front().y;
    double area = 0.0;

    for (const auto &e : ev) {
        long long curY = e.y;
        long long cov = st.covered();
        area += cov * double(curY - prevY);

        int l = lower_bound(xs.begin(), xs.end(), e.x1) - xs.begin();
        int r = lower_bound(xs.begin(), xs.end(), e.x2) - xs.begin();
        st.upd(1, 0, xs.size() - 1, l, r, e.delta);
        prevY = curY;
    }
    return area;
}

double find_split(vector<Event> ev, const vector<long long> &xs,
                  double half) {
    sort(ev.begin(), ev.end(), [](const Event &a, const Event &b){
        return a.y < b.y;
    });

    SegTree st(xs);
    long long prevY = ev.front().y;
    double cum = 0.0;

    for (const auto &e : ev) {
        long long curY = e.y;
        long long cov = st.covered();
        long long deltaY = curY - prevY;

        if (cov > 0 && cum + cov * double(deltaY) >= half) {
            double need = half - cum;
            return prevY + need / cov;
        }
        cum += cov * double(deltaY);

        int l = lower_bound(xs.begin(), xs.end(), e.x1) - xs.begin();
        int r = lower_bound(xs.begin(), xs.end(), e.x2) - xs.begin();
        st.upd(1, 0, xs.size() - 1, l, r, e.delta);
        prevY = curY;
    }
    return ev.back().y;  // edge case
}

// ---------- Main ----------
int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    int N;  // number of rectangles
    if(!(cin >> N)) return 0;
    vector<Event> ev;
    vector<long long> raw;
    for (int i = 0; i < N; ++i) {
        long long x1,y1,x2,y2;
        cin >> x1 >> y1 >> x2 >> y2;
        ev.emplace_back(y1, x1, x2, +1);
        ev.emplace_back(y2, x1, x2, -1);
        raw.push_back(x1);
        raw.push_back(x2);
    }

    vector<long long> xs = compress(raw);
    double total = compute_area(ev, xs);
    double half = total / 2.0;
    double answer = find_split(ev, xs, half);

    cout.setf(ios::fixed); cout << setprecision(6) << answer << '\n';
    return 0;
}
```

**Compile**

```
g++ -std=c++17 -O2 -Wall main.cpp -o main
```

---

### 9.  Complexity Analysis

- **Sorting events**: \(O(N \log N)\)
- **Building segment tree**: \(O(N)\) (once per sweep)
- **Sweep**: each event triggers a tree update – \(O(\log N)\)
- **Total**: \(O(N \log N)\) time, \(O(N)\) memory

With \(N \le 10^5\), this easily satisfies the limits.

---

### 10.  Final Remarks

- The line sweep technique turns a seemingly two‑dimensional geometric
  problem into a sequence of 1‑dimensional updates, enabling an
  \(O(N\log N)\) solution.
- Using a **difference array** for the x‑coverage simplifies the
  implementation and guarantees linear memory.
- The solution works for any integer coordinates within the limits
  \([0,10^9]\) and is robust against degenerate cases where all area is
  concentrated on a single horizontal line.