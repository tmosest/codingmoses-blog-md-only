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
pair  i  = ( nums1[i] , nums2[i] )
sum   i  = nums1[i] + nums2[i]
```

A query is a pair `(x , y)` and we have to find

```
max{ nums1[i] + nums2[i] }      (1)
   i
such that   nums1[i] ≥ x  and  nums2[i] ≥ y
```

If no index satisfies the two inequalities the answer is `‑1`.

The arrays can contain up to `2·10⁵` elements, so we need an
`O((n+m) log n)` algorithm  
(`n` – length of the two input arrays,
`m` – number of queries).

--------------------------------------------------------------------

#### 1.  Offline processing

For a fixed `x` only indices with `nums1[i] ≥ x` are useful.
If we sort the pairs by `nums1` in **decreasing** order and also sort the
queries by `x` in decreasing order, we can scan both lists once:

```
current x = 0
for every query (x , y) in decreasing x
        add all pairs whose nums1 ≥ x   to a data structure
        answer the query with the data structure
```

When we move from one query to the next we only **add** new pairs,
never delete them.  
The whole process is therefore “offline” – we first sort the input
and then run through it.

--------------------------------------------------------------------

#### 2.  What must the data structure support?

After the “add” step the data structure contains all pairs
with `nums1 ≥ current x`.  
We have to answer

```
maximum sum among all indices with nums2 ≥ y          (2)
```

The pairs are inserted in the order of decreasing `nums1`, therefore
after the first insertion a pair may later be inserted again with a
larger `nums1` – we must keep the **maximum** sum for every value of
`nums2`.

--------------------------------------------------------------------

#### 3.  Coordinate compression of `nums2`

Only the values of `nums2` are used as keys in the data structure.
We replace every value by its rank in the sorted list of distinct
`nums2` values.

```
uniq = sorted list of all distinct nums2 values      (ascending)
k    = uniq.len()          – number of different keys
```

For a value `v` let

```
pos(v)   = index of v in uniq  (0 … k‑1)
rev(v)   = k-1 - pos(v)        (reversed index)
```

If we store the data structure on the **reversed** indices,
querying all `nums2 ≥ y` (positions `pos(y) … k‑1`)
becomes a query on the prefix `[0 … rev(y)]` – exactly what a Fenwick
tree (Binary Indexed Tree) supports.



--------------------------------------------------------------------

#### 4.  Fenwick tree for maximum

A Fenwick tree can be used for prefix maximum in `O(log k)`:

```
update(idx , value)          // point update – keep the maximum
query_prefix(idx)            // maximum on [0 … idx]
```

All values are initialised to `‑1` (no pair inserted yet).

--------------------------------------------------------------------

#### 5.  Complete algorithm

```
pairs = [(nums1[i], nums2[i]) for i in 0..n]
sort pairs  by first element (nums1)   descending

uniq = sorted distinct nums2 values
for each pair (a,b) create rev_idx(b) = k-1 - pos(b)

queries = [(x, y, original_index) for each query]
sort queries by x   descending

Fenwick tree T of size k, all entries = -1
i = 0                                 // pointer in pairs

for each query (x, y, id) in sorted order
        while i < n and pairs[i].0 >= x
                let (a,b)   = pairs[i]
                let rev     = rev_idx(b)
                let sum     = a + b
                T.update(rev, sum)      // keep the maximum
                i += 1

        // answer the query
        find the first position p in uniq with uniq[p] >= y
        if such p does not exist          → answer[id] = -1
        else
                rev_start = k-1 - p
                best = T.query_prefix(rev_start)
                answer[id] = if best < 0 { -1 } else { best }
```

--------------------------------------------------------------------

#### 6.  Correctness Proof  

We prove that the algorithm outputs the correct answer for every
query.

---

##### Lemma 1  
After processing all pairs with `nums1[i] ≥ X` (where `X` is the
current query's `x`), the Fenwick tree at position
`rev_idx(v)` stores

```
max{ nums1[i] + nums2[i] }  over all indices i with
        nums1[i] ≥ X  and  nums2[i] = v
```

*Proof.*

Pairs are inserted in the order of decreasing `nums1`.  
When a pair `(a,b)` is processed, its sum `a+b` is written to the
Fenwick tree at the position `rev_idx(b)`.
If later another pair with the same `nums2` value is processed,
`update` keeps the larger sum.  
Thus, after all pairs with `nums1 ≥ X` are inserted, the tree
contains the maximum sum for every `nums2` value that satisfies the
first inequality. ∎



##### Lemma 2  
For a query `(x,y)` let `p` be the smallest index with
`uniq[p] ≥ y`.  
Then the value returned by `T.query_prefix(k-1-p)` equals

```
max{ nums1[i] + nums2[i] }     (3)
   i
such that   nums1[i] ≥ x  and  nums2[i] ≥ y
```

*Proof.*

`p` is the first distinct `nums2` value that is at least `y`.  
All indices with `nums2 ≥ y` have compressed positions `pos ≥ p`.
Their reversed indices are `rev ≤ k-1-p`.  
Because of Lemma&nbsp;1 the tree contains the maximum sum for every
such position, and `query_prefix(k-1-p)` returns the maximum over
exactly those positions.  
Consequently the returned value is the maximum sum among all indices
satisfying both inequalities. ∎



##### Lemma 3  
If no index satisfies the inequalities of a query,
the algorithm outputs `‑1`.

*Proof.*

In that case the Fenwick tree contains only values `< 0` for all
positions `rev ≤ k-1-p`.  
`query_prefix` therefore returns `‑1`.  
The algorithm translates that to the output `‑1`. ∎



##### Theorem  
For every query `(x,y)` the algorithm outputs

```
max{ nums1[i] + nums2[i] }  over all i with  nums1[i] ≥ x  and  nums2[i] ≥ y,
```

or `‑1` if such an `i` does not exist.

*Proof.*

Consider an arbitrary query `(x,y)`.

*Insertion phase*  
All pairs with `nums1 ≥ x` are inserted into the Fenwick tree.
By Lemma&nbsp;1 the tree now contains the maximum sum for every
`nums2` value among those pairs.

*Query phase*  
`p` is the first distinct `nums2` value that is at least `y`.  
If it does not exist, by definition no index can satisfy
`nums2[i] ≥ y`; the algorithm outputs `‑1` (Lemma&nbsp;3).  
Otherwise `T.query_prefix(k-1-p)` returns the maximum sum over all
indices with `nums2 ≥ y` (Lemma&nbsp;2).  
Because only indices with `nums1 ≥ x` were inserted, this sum also
fulfils `nums1[i] ≥ x`.  
Thus the returned value is exactly the expression in (1).

Therefore every query is answered correctly. ∎



--------------------------------------------------------------------

#### 7.  Complexity Analysis

```
sorting pairs            :  O(n log n)
sorting queries          :  O(m log m)
building uniq list       :  O(n log n)
for each of n pairs      :  O(log n)  (Fenwick update)
for each of m queries    :  O(log n)  (binary search + Fenwick query)

Total time                :  O((n+m) log n)
Memory consumption        :  O(n + k)   (k = number of distinct nums2 ≤ n)
```

--------------------------------------------------------------------

#### 8.  Reference Implementation  (Rust 1.56)

```rust
use std::cmp::Ordering;
use std::collections::HashMap;
use std::io::{self, Read};

/// Fenwick tree that keeps the maximum value on a prefix
struct Fenwick {
    n: usize,
    data: Vec<i64>, // values are sums, -1 means “not inserted yet”
}

impl Fenwick {
    fn new(n: usize) -> Self {
        Fenwick {
            n,
            data: vec![-1; n],
        }
    }

    // point update: keep the maximum
    fn update(&mut self, mut idx: usize, val: i64) {
        while idx < self.n {
            if self.data[idx] < val {
                self.data[idx] = val;
            }
            idx |= idx + 1; // move to parent
        }
    }

    // maximum on prefix [0 .. idx]
    fn query_prefix(&self, mut idx: usize) -> i64 {
        let mut res = -1;
        while idx > 0 {
            if self.data[idx] > res {
                res = self.data[idx];
            }
            idx &= idx + 1; // move to parent
        }
        // handle the last element separately (idx == 0)
        if self.data[0] > res {
            res = self.data[0];
        }
        res
    }
}

fn main() {
    // ---------- read input ----------
    let mut input = String::new();
    io::stdin().read_to_string(&mut input).unwrap();
    let mut it = input.split_whitespace();

    let n: usize = it.next().unwrap().parse().unwrap();
    let m: usize = it.next().unwrap().parse().unwrap();

    let mut nums1: Vec<i64> = Vec::with_capacity(n);
    let mut nums2: Vec<i64> = Vec::with_capacity(n);
    for _ in 0..n {
        nums1.push(it.next().unwrap().parse::<i64>().unwrap());
    }
    for _ in 0..n {
        nums2.push(it.next().unwrap().parse::<i64>().unwrap());
    }

    // pairs
    let mut pairs: Vec<(i64, i64)> = Vec::with_capacity(n);
    for i in 0..n {
        pairs.push((nums1[i], nums2[i]));
    }
    // sort by nums1 descending
    pairs.sort_by(|a, b| b.0.cmp(&a.0));

    // ---------- coordinate compression of nums2 ----------
    let mut uniq: Vec<i64> = nums2.clone();
    uniq.sort_unstable();
    uniq.dedup();
    let k = uniq.len();

    // map from original nums2 value to reversed index
    let mut rev_map: HashMap<i64, usize> = HashMap::new();
    for (pos, &val) in uniq.iter().enumerate() {
        let rev = k - 1 - pos;
        rev_map.insert(val, rev);
    }

    // ---------- read queries ----------
    let mut queries: Vec<(i64, i64, usize)> = Vec::with_capacity(m);
    for idx in 0..m {
        let x: i64 = it.next().unwrap().parse().unwrap();
        let y: i64 = it.next().unwrap().parse().unwrap();
        queries.push((x, y, idx));
    }
    // sort by x descending
    queries.sort_by(|a, b| b.0.cmp(&a.0));

    // ---------- Fenwick tree ----------
    let mut fw = Fenwick::new(k);

    // ---------- process ----------
    let mut answers: Vec<i64> = vec![-1; m];
    let mut ptr = 0usize; // pointer in pairs

    for &(x, y, id) in &queries {
        // insert all pairs with nums1 >= x
        while ptr < n && pairs[ptr].0 >= x {
            let (a, b) = pairs[ptr];
            let rev = rev_map[&b];
            let sum = a + b;
            fw.update(rev, sum);
            ptr += 1;
        }

        // binary search for first uniq[pos] >= y
        let pos_opt = uniq
            .binary_search_by(|v| {
                if *v < y {
                    Ordering::Less
                } else {
                    Ordering::Greater
                }
            })
            .or_else(|pos| if uniq[pos] >= y { Ok(pos) } else { Err(k) });

        let ans = match pos_opt {
            Err(_) => -1, // no nums2 >= y
            Ok(pos) => {
                let rev_start = k - 1 - pos;
                let best = fw.query_prefix(rev_start);
                if best < 0 { -1 } else { best }
            }
        };
        answers[id] = ans;
    }

    // ---------- output ----------
    let mut out = String::new();
    for &v in &answers {
        out.push_str(&format!("{}\n", v));
    }
    print!("{}", out);
}
```

The program follows exactly the algorithm proven correct above and
conforms to the Rust 1.56 standard library.  It uses only
standard containers and runs in `O((n+m) log n)` time with
`O(n)` memory.