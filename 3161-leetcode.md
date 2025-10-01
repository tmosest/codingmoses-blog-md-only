---
title: LeetCode 3161. Block Placement Queries - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

For every position `i   (0 ≤ i ≤ 50000)` we know whether the position is
*free* (value `1`) or contains an *obstacle* (value `0`).

*   a query of type **1** – `1  x`  
    puts an obstacle on position `x`.  
    The answer for this query is always **true** – the obstacle was built.

*   a query of type **2** – `2  x  sz`  
    asks whether a block of length `sz` can be put in the row such that
    its last cell is **not farther than `x`**.

The block must lie completely in the interval `[0 , x]`
(other positions – especially obstacles after `x` – are irrelevant).
So the question is

```
is the length of the longest consecutive sequence of free cells in
[0 , x] at least sz ?
```

If it is, the block can be chosen inside that free sequence and the
answer is **true**.



--------------------------------------------------------------------

#### 1.   Data structure – segment tree

For every node that represents a segment `[l , r]` we store

| field | meaning |
|-------|---------|
| `len` | length of the segment `r – l + 1` |
| `pref` | longest prefix of free cells (`1`s) inside the segment |
| `suff` | longest suffix of free cells inside the segment |
| `best` | longest *contiguous* subsequence of free cells inside the segment |

The tree is built for all 50 001 positions, initially all positions are
free (`value = 1`).

For a leaf (a single position)

```
len  = 1
pref = suff = best = value   (1 or 0)
```

For an internal node we merge the two children `L` and `R`

```
pref = (L.pref == L.len) ? L.len + R.pref : L.pref
suff = (R.suff == R.len) ? R.len + L.suff : R.suff
best = max( L.best , R.best , L.suff + R.pref )
```

All values are integers – `int` is more than enough.



--------------------------------------------------------------------

#### 2.   Operations

*Point update* – setting one position to an obstacle  
`update(pos, 0)`  
The leaf for `pos` gets the value `0`; the path to the root is
re‑merged – `O(log N)`.

*Range query* – maximum free block length inside `[0 , x]`  
`query(0, x)` returns a node.
If `node.best ≥ sz` the answer is **true**, otherwise **false** – `O(log N)`.



--------------------------------------------------------------------

#### 3.   Algorithm
```
build a segment tree of size 50001, all values = 1
for every query i = 1 … Q
    read type
    if type == 1
        read x
        update(x, 0)               // put obstacle
        answer[i] = true
    else            // type == 2
        read x , sz
        answer[i] = ( query(0, x).best >= sz )
output all answers
```

The answers for type 1 queries are always **true**,
for type 2 queries the segment tree decides.



--------------------------------------------------------------------

#### 4.   Correctness Proof  

We prove that the algorithm outputs the correct answer for every query.

---

##### Lemma 1  
For every node of the segment tree the field `best`
equals the length of the longest contiguous free subsequence
inside the represented segment.

**Proof.**

Induction over the tree height.

*Base – leaf.*  
The segment contains only one cell.
If it is free, the longest free block inside it has length `1`;
if it contains an obstacle, no free cell exists – length `0`.
The leaf is built exactly that way, so the statement holds.

*Induction step.*  
Assume it is true for the two children `L` and `R`.  
Any contiguous free subsequence in the parent
either lies completely inside `L`, completely inside `R`,
or spans the boundary and consists of the suffix of `L` followed by
the prefix of `R`.  
The merge formula
`best = max(L.best, R.best, L.suff+R.pref)`
therefore stores exactly the maximum length of such a subsequence. ∎



##### Lemma 2  
After performing a point update `update(pos,0)`
the values stored in all affected nodes are still correct.

**Proof.**

A leaf gets value `0`.  
All internal nodes on the path from that leaf to the root are
re‑merged from their children, and each merge uses only the children’s
fields (which are correct by the induction hypothesis).
Therefore after the merge the fields of every node on the path are
correct again. ∎



##### Lemma 3  
`query(0, x)` returns a node whose field `best`
equals the length of the longest contiguous sequence of free cells
inside the interval `[0 , x]`.

**Proof.**

The query returns a node that represents the union of all segments
fully contained in `[0 , x]`.  
By Lemma&nbsp;1 every such node contains the correct `best` value for
its own segment.
During the query the merge operation is applied exactly as in the tree
construction, so the resulting node is the merge of all these
segments and therefore its `best` equals the maximum over all
sub‑segments, i.e. the maximum free block length inside `[0 , x]`. ∎



##### Lemma 4  
For a type‑2 query `2 x sz` the algorithm outputs **true**
iff a block of length `sz` can be placed with its last cell
not farther than `x`.

**Proof.**

*If part.*  
If the algorithm outputs **true** then, by Lemma&nbsp;3,
`best ≥ sz`.  
So there exists a contiguous sequence of free cells of length at least
`sz` entirely inside `[0 , x]`.  
Choosing the first `sz` cells of this sequence yields a valid block.

*Only‑if part.*  
Assume a block of length `sz` can be placed ending at a cell
`≤ x`.  
All its cells are free, thus all of them lie inside the interval `[0 , x]`.
Consequently the longest free sequence inside `[0 , x]`
has length at least `sz`.  
By Lemma&nbsp;3 the query will find `best ≥ sz` and the algorithm
answers **true**. ∎



##### Theorem  
The algorithm prints the correct answer for every query.

**Proof.**

Type 1 queries: the algorithm prints **true**, which is the required
answer.

Type 2 queries: by Lemma&nbsp;4 the algorithm prints **true** exactly
when a valid block exists, otherwise **false**.  
Thus all answers are correct. ∎



--------------------------------------------------------------------

#### 4.   Complexity Analysis

```
building the tree:      O(N)   = O(5·10^4)
each point update:      O(log N)
each range query:       O(log N)
```

With `Q` queries

```
time   :  O((Q + number_of_type1) · log N)   ≤  O(Q · log 50001)
memory :  O(N)  =  O(5·10^4) integers  (≈ 4 MB)
```

Both limits are easily inside the given constraints.



--------------------------------------------------------------------

#### 5.   Reference Implementation  (GNU‑C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

/* ---------- Segment tree for longest free block ---------- */
struct SegTree {
    struct Node {
        int len;     // length of the segment
        int pref;    // longest prefix of 1's
        int suff;    // longest suffix of 1's
        int best;    // longest contiguous 1's inside the segment
    };
    int n;                 // number of positions (50001)
    vector<Node> st;       // segment tree array

    SegTree(int _n) : n(_n) {
        st.resize(4 * n);
        build(1, 0, n - 1);           // all positions free initially
    }

    /* merge two child nodes into a parent node */
    Node merge(const Node &L, const Node &R) const {
        Node res;
        res.len = L.len + R.len;
        res.pref = (L.pref == L.len) ? L.len + R.pref : L.pref;
        res.suff = (R.suff == R.len) ? R.len + L.suff : R.suff;
        res.best = max({L.best, R.best, L.suff + R.pref});
        return res;
    }

    /* build the tree, all leaves have value 1 */
    void build(int p, int l, int r) {
        if (l == r) {
            st[p] = {1, 1, 1, 1};
            return;
        }
        int m = (l + r) / 2;
        build(p << 1, l, m);
        build(p << 1 | 1, m + 1, r);
        st[p] = merge(st[p << 1], st[p << 1 | 1]);
    }

    /* point update: set position idx to val (0 or 1) */
    void update(int idx, int val, int p, int l, int r) {
        if (l == r) {
            st[p] = {1, val, val, val};
            return;
        }
        int m = (l + r) / 2;
        if (idx <= m) update(idx, val, p << 1, l, m);
        else          update(idx, val, p << 1 | 1, m + 1, r);
        st[p] = merge(st[p << 1], st[p << 1 | 1]);
    }
    void update(int idx, int val) { update(idx, val, 1, 0, n - 1); }

    /* query on [L , R], returns a node (empty node for out‑of‑range) */
    Node query(int L, int R, int p, int l, int r) const {
        if (R < l || r < L) return {0, 0, 0, 0};      // empty intersection
        if (L <= l && r <= R) return st[p];
        int m = (l + r) / 2;
        Node left  = query(L, R, p << 1, l, m);
        Node right = query(L, R, p << 1 | 1, m + 1, r);
        return merge(left, right);
    }
    Node query(int L, int R) const { return query(L, R, 1, 0, n - 1); }
};

/* ------------------------ main ------------------------ */
int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    const int MAX_POS = 50001;            // positions 0 … 50000
    SegTree seg(MAX_POS);

    int Q;
    if (!(cin >> Q)) return 0;            // no input

    vector<string> ans;
    ans.reserve(Q);

    for (int i = 0; i < Q; ++i) {
        int type;
        cin >> type;
        if (type == 1) {                   // build obstacle
            int x; cin >> x;
            if (0 <= x && x < MAX_POS) seg.update(x, 0);
            ans.push_back("true");         // always successful
        } else {                           // type 2
            int x, sz;
            cin >> x >> sz;
            if (x < 0) { ans.push_back("false"); continue; }
            if (x >= MAX_POS) x = MAX_POS - 1;          // safety
            auto res = seg.query(0, x);
            ans.push_back(res.best >= sz ? "true" : "false");
        }
    }

    for (size_t i = 0; i < ans.size(); ++i) {
        cout << ans[i];
        if (i + 1 < ans.size()) cout << '\n';
    }
    return 0;
}
```

The program follows exactly the algorithm proven correct above
and is fully compliant with the GNU++17 compiler.