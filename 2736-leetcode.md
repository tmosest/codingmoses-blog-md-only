---
title: LeetCode 2736. Maximum Sum Queries - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For every index `i`

```
point i = (a1[i] , a2[i])          0 ≤ i < n
sum i   = a1[i] + a2[i]
```

For a query `(x , y)` we need

```
maximum sum i   with   a1[i] ≥ x   and   a2[i] ≥ y
```

If no such index exists the answer is `-1`.

The problem is a two‑dimensional dominance query:

```
points : (a1[i] , a2[i])            value : a1[i] + a2[i]
query  : (x , y)  – all points with   x ≤ a1  and   y ≤ a2
answer : maximum value among those points
```

--------------------------------------------------------------------

#### 1.   Offline processing

`a1` and `a2` are up to `2·10^5`, therefore an
`O((n + q) log n)` algorithm is required.

```
sort all indices by a1   (descending)
sort all queries by x    (descending)

while processing the queries:
        add all points with a1 ≥ current query.x
        answer the query with a data structure that keeps
        the maximum sum of all already added points
        with a2 ≥ current query.y
```

When all points with `a1 ≥ x` are inserted,
only the restriction `a2 ≥ y` remains.
The remaining problem is a one‑dimensional
*range maximum* query over the `a2` values.



--------------------------------------------------------------------

#### 2.   Compression of `a2`

Only the values of `a2` that really appear are needed.

```
unique_a2   = sorted list of all distinct a2 values
index[a2]   = position of a2 inside unique_a2   (0 … m-1)
```

`m = unique_a2.len()` – the number of different `a2` values.



--------------------------------------------------------------------

#### 3.   Segment Tree for maximum

The segment tree stores for every compressed `a2` index the
maximum sum of all inserted points that have exactly this `a2`.
Only the *maximum* value inside a range is needed.

```
update(position , new_value)      O(log m)
query( left , right )             O(log m)
```

All values are initialised with `-1`
(`-1` is smaller than every possible sum, therefore it represents
“no point yet”).

For a query with lower bound `y`:

```
lb = first index with unique_a2[lb] ≥ y
if lb == m          → no a2 is large enough → answer -1
else                → answer = query(lb , m-1)
```

The tree automatically ignores all points whose `a2` is
smaller than the requested `y`.



--------------------------------------------------------------------

#### 4.   Correctness Proof  

We prove that the algorithm prints the required maximum sum
for every query.

---

##### Lemma 1  
During the processing of a query `q = (x , y)`  
every point `p = (a1 , a2)` with `a1 ≥ x` is inserted into the
segment tree, and no point with `a1 < x` is inserted.

**Proof.**

Points are sorted by `a1` in decreasing order.
A pointer `pos` moves over this array.
While `pos < n` and `points[pos].a1 ≥ x`
the point is inserted and `pos` is increased.
Consequently, after the while loop

* all points with `a1 ≥ x` are inserted,
* all points with `a1 < x` are still not inserted. ∎



##### Lemma 2  
After all insertions for a query `q`,
the segment tree value at position `k` equals

```
max { a1[i] + a2[i] |  i already inserted and  a2_index[i] = k }
```

**Proof.**

When a point `i` is inserted,
`update(k , a1[i] + a2[i])` is executed.
The update keeps the maximum of all values written to position `k`,
therefore after processing all inserted points the stored value is
exactly the maximum sum over all of them with that `a2_index`. ∎



##### Lemma 3  
For a query `q = (x , y)` let  
`lb = first index with unique_a2[lb] ≥ y`.  
`query(lb , m-1)` returns

```
max { a1[i] + a2[i] |  i inserted for q  and  a2[i] ≥ y }
```

**Proof.**

By Lemma&nbsp;1 only points with `a1 ≥ x` are inserted.
By Lemma&nbsp;2 each tree position contains the maximum sum among
inserted points with this exact `a2_index`.  
The query asks for the maximum over the range `lb … m-1`,
i.e. over all compressed indices whose real `a2` value is at least
`y`.  
No inserted point with `a2 < y` contributes to this range, therefore
the returned value equals the maximum sum among all already
inserted points satisfying both restrictions of the query. ∎



##### Lemma 4  
`query(lb , m-1)` is `-1` iff no inserted point satisfies `a2 ≥ y`.

**Proof.**

If no inserted point has `a2 ≥ y`,
every tree position in the queried range has value `-1`
(Lemma&nbsp;2), thus the maximum over this range is `-1`.  
Conversely, if at least one inserted point has `a2 ≥ y`,
the corresponding tree position contains a value ≥ `0`,
therefore the maximum of the queried range is not `-1`. ∎



##### Lemma 4 (continued)  
If `lb == m` the algorithm outputs `-1`,
otherwise it outputs the maximum sum of all inserted points with
`a2 ≥ y`.

**Proof.**

`lb == m` means there is no compressed index whose real `a2`
value is at least `y`.  
Consequently no inserted point can satisfy `a2 ≥ y`, hence by
definition the answer is `-1`, which the algorithm prints.

If `lb < m`, by Lemma&nbsp;3 the segment tree query returns the
maximum sum of all inserted points with `a2 ≥ y`. ∎



##### Lemma 5  
For a query `q = (x , y)`  
every index `i` that satisfies the query conditions
(`a1[i] ≥ x` and `a2[i] ≥ y`) is inserted before the answer is
computed.

**Proof.**

Directly from Lemma&nbsp;1:
every point with `a1 ≥ x` is inserted before the answer,
and `a2[i] ≥ y` is handled by the tree query. ∎



##### Lemma 6  
For a query `q = (x , y)` the algorithm outputs the maximum
possible sum over *all* indices that satisfy the query.

**Proof.**

Let `S` be the set of all indices satisfying the query.
By Lemma&nbsp;5 all indices in `S` are inserted when the answer is
computed.
By Lemma&nbsp;4 the algorithm returns the maximum sum among
those inserted indices.
Because the set of inserted indices is exactly `S`, the returned
value equals `max { a1[i] + a2[i] | i ∈ S }`.  
If `S` is empty the algorithm outputs `-1`. ∎



##### Theorem  
For every query the program prints the required maximum sum, and
`-1` iff no index satisfies the query constraints.

**Proof.**

Directly from Lemma&nbsp;6. ∎



--------------------------------------------------------------------

#### 5.   Complexity Analysis

```
compression of a2          :  O(n log n)
sorting points & queries   :  O(n log n + q log q)
segment tree updates/query  :  O((n + q) log n)
memory consumption         :  O(n + m)  (m ≤ n)
```

The program fulfils the required limits.



--------------------------------------------------------------------

#### 6.   Reference Implementation  (Rust 1.56)

```rust
use std::cmp::max;
use std::collections::HashMap;
use std::io::{self, Read};

/// Point that will be inserted into the tree
#[derive(Clone)]
struct Point {
    a1: i64,
    a2: i64,
}

/// Query stored with its original position
#[derive(Clone)]
struct Query {
    x: i64,
    y: i64,
    id: usize,
}

/// Segment tree for range maximum
struct SegTree {
    size: usize,
    data: Vec<i64>,          // 1‑based indexing
}

impl SegTree {
    fn new(n: usize) -> Self {
        SegTree {
            size: n,
            data: vec![-1; 4 * n + 4],
        }
    }

    /// point update: tree[idx] = max(tree[idx], val)
    fn update(&mut self, node: usize, l: usize, r: usize, idx: usize, val: i64) {
        if l == r {
            if self.data[node] < val {
                self.data[node] = val;
            }
            return;
        }
        let mid = (l + r) / 2;
        if idx <= mid {
            self.update(node * 2, l, mid, idx, val);
        } else {
            self.update(node * 2 + 1, mid + 1, r, idx, val);
        }
        self.data[node] = max(self.data[node * 2], self.data[node * 2 + 1]);
    }

    /// range maximum on [ql, qr]
    fn query(&self, node: usize, l: usize, r: usize, ql: usize, qr: usize) -> i64 {
        if ql > r || qr < l {
            return -1;
        }
        if ql <= l && r <= qr {
            return self.data[node];
        }
        let mid = (l + r) / 2;
        let left = self.query(node * 2, l, mid, ql, qr);
        let right = self.query(node * 2 + 1, mid + 1, r, ql, qr);
        max(left, right)
    }
}

fn main() {
    /* ---------- read all input ---------- */
    let mut input = String::new();
    io::stdin().read_to_string(&mut input).unwrap();
    let mut it = input.split_whitespace().map(|s| s.parse::<i64>().unwrap());

    let n = it.next().unwrap() as usize;

    let mut a1: Vec<i64> = Vec::with_capacity(n);
    let mut a2: Vec<i64> = Vec::with_capacity(n);
    for _ in 0..n {
        a1.push(it.next().unwrap());
    }
    for _ in 0..n {
        a2.push(it.next().unwrap());
    }

    let q_cnt = it.next().unwrap() as usize;
    let mut queries: Vec<Query> = Vec::with_capacity(q_cnt);
    for id in 0..q_cnt {
        let x = it.next().unwrap();
        let y = it.next().unwrap();
        queries.push(Query { x, y, id });
    }

    /* ---------- compression of a2 ---------- */
    let mut unique_a2 = a2.clone();
    unique_a2.sort_unstable();
    unique_a2.dedup();
    let m = unique_a2.len();                     // number of different a2

    let mut index_map: HashMap<i64, usize> = HashMap::new();
    for (i, &val) in unique_a2.iter().enumerate() {
        index_map.insert(val, i);
    }

    /* ---------- sort points and queries ---------- */
    let mut points: Vec<Point> = Vec::with_capacity(n);
    for i in 0..n {
        points.push(Point { a1: a1[i], a2: a2[i] });
    }
    points.sort_by(|p1, p2| p2.a1.cmp(&p1.a1));    // descending a1

    let mut queries_sorted = queries.clone();
    queries_sorted.sort_by(|q1, q2| q2.x.cmp(&q1.x)); // descending x

    /* ---------- segment tree ---------- */
    let mut seg_tree = SegTree::new(m);

    let mut pos = 0usize;          // next point that is not inserted
    let mut answer = vec![-1i64; q_cnt];

    for q in queries_sorted.iter() {
        /* insert all points with a1 >= q.x */
        while pos < n && points[pos].a1 >= q.x {
            let idx_a2 = *index_map.get(&points[pos].a2).unwrap();
            let sum = points[pos].a1 + points[pos].a2;
            seg_tree.update(1, 0, m - 1, idx_a2, sum);
            pos += 1;
        }

        /* find lower bound for y */
        let lb = match unique_a2.binary_search(&q.y) {
            Ok(idx) => idx,
            Err(idx) => idx,
        };

        if lb < m {
            let res = seg_tree.query(1, 0, m - 1, lb, m - 1);
            answer[q.id] = res;
        } else {
            answer[q.id] = -1;
        }
    }

    /* ---------- output ---------- */
    let mut out = String::new();
    for val in answer {
        out.push_str(&format!("{}\n", val));
    }
    print!("{}", out);
}
```

The program follows exactly the algorithm proven correct above
and is fully compatible with Rust 1.56.