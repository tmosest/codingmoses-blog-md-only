---
title: LeetCode 3526. Range XOR Queries with Subarray Reversals - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 3526 – Range XOR Queries with Subarray Reversals  
**Hard | Public | 1 ≤ nums.length, queries.length ≤ 10⁵**

---

## 1. Problem Overview  

You are given an array `nums` and a stream of queries.  
Three query types exist:

| Code | Meaning | Parameters |
|------|---------|------------|
| `1` | Point update: `nums[index] = value` | `[1, index, value]` |
| `2` | Range XOR: return `nums[left] ⊕ … ⊕ nums[right]` | `[2, left, right]` |
| `3` | Sub‑array reversal: reverse the slice `nums[left…right]` | `[3, left, right]` |

Return an array containing the answers of every type‑2 query in the order they were processed.

The brute‑force solution runs in `O(n)` per query – far too slow for the 10⁵ bound.

---

## 2. Why a Tree is the Right Tool  

All operations involve **contiguous segments** of the array.  
A classic data structure for such problems is the *implicit* binary tree:

* **Implicit Treap** – a randomized BST that stores elements by *position* instead of key.  
* **Implicit Splay** – self‑balancing by rotations on access.  
* **Implicit AVL** – deterministic balance, but a bit more boilerplate.  

The key properties we need to augment:

1. **Size** – to split/merge by index.  
2. **XOR of the subtree** – to answer range XOR in `O(1)` after a split.  
3. **Lazy reverse flag** – to reverse a whole segment in `O(log n)` by toggling a flag instead of touching every node.  

All operations (`split`, `merge`, `update`, `reverse`, `rangeXOR`) become `O(log n)` on average, giving an overall `O((n+q) log n)` solution.

---

## 3. High‑Level Algorithm

1. **Build** the implicit tree from the initial `nums`.  
   *Insert* every element at the end (`insert(i, nums[i])`), or build in `O(n)` by successive merges.

2. For each query:  

   * **Update**  
     * Split into `[0, index)`, `[index]`, `[index+1, n)`  
     * Change the middle node’s value, recalc its XOR.  
     * Merge back.

   * **Range XOR**  
     * Split into `[0, left)`, `[left, right]`, `[right+1, n)`  
     * The XOR of the middle part is the answer.  
     * Merge back.

   * **Reverse**  
     * Split into `[0, left)`, `[left, right]`, `[right+1, n)`  
     * Toggle the reverse flag of the middle part.  
     * Merge back.

All splits/merges keep the tree balanced on average because the priority (or rotation) is randomized.

---

## 4. Code

Below you’ll find clean, ready‑to‑copy implementations for **Java**, **Python**, and **C++**.

> **Tip** – In Python you’ll need `sys.setrecursionlimit(1 << 25)` or iterative splits/merges if your recursion depth is a problem.  
> In C++ use `std::mt19937 rng` for priorities.  
> In Java use `ThreadLocalRandom.current()`.

---

### 4.1 Java – Implicit Treap

```java
import java.util.*;
import java.util.concurrent.ThreadLocalRandom;

public class Solution {
    /* ---------- Node ---------- */
    private static class Node {
        int val;          // stored value
        int xor;          // xor of the subtree
        int size;         // size of subtree
        int prio;         // random priority
        boolean rev;      // lazy reverse flag
        Node left, right;

        Node(int v) {
            this.val = v;
            this.xor = v;
            this.size = 1;
            this.prio = ThreadLocalRandom.current().nextInt();
            this.rev = false;
        }
    }

    /* ---------- Helpers ---------- */
    private static int size(Node t)   { return t == null ? 0 : t.size; }
    private static int xor(Node t)    { return t == null ? 0 : t.xor; }

    private static void push(Node t) {
        if (t != null && t.rev) {
            t.rev = false;
            Node tmp = t.left; t.left = t.right; t.right = tmp;
            if (t.left  != null) t.left.rev  ^= true;
            if (t.right != null) t.right.rev ^= true;
            // XOR is symmetric, no need to recompute
        }
    }

    private static void upd(Node t) {
        if (t != null) {
            t.size = 1 + size(t.left) + size(t.right);
            t.xor  = t.val ^ xor(t.left) ^ xor(t.right);
        }
    }

    /* ---------- Split / Merge ---------- */
    // split by size: left has first k elements
    private static Node[] split(Node t, int k) {
        if (t == null) return new Node[]{null, null};
        push(t);
        if (k <= size(t.left)) {
            Node[] res = split(t.left, k);
            t.left = res[1];
            upd(t);
            return new Node[]{res[0], t};
        } else {
            Node[] res = split(t.right, k - size(t.left) - 1);
            t.right = res[0];
            upd(t);
            return new Node[]{t, res[1]};
        }
    }

    private static Node merge(Node a, Node b) {
        if (a == null) return b;
        if (b == null) return a;
        push(a); push(b);
        if (a.prio > b.prio) {
            a.right = merge(a.right, b);
            upd(a);
            return a;
        } else {
            b.left = merge(a, b.left);
            upd(b);
            return b;
        }
    }

    /* ---------- Operations ---------- */
    private Node root;

    // Build tree from array
    void build(int[] arr) {
        for (int v : arr) root = merge(root, new Node(v));
    }

    void update(int idx, int val) {
        Node[] a = split(root, idx);
        Node[] b = split(a[1], 1);
        b[0].val = val;
        upd(b[0]);
        root = merge(a[0], merge(b[0], b[1]));
    }

    int rangeXor(int l, int r) {
        Node[] a = split(root, l);
        Node[] b = split(a[1], r - l + 1);
        int res = xor(b[0]);
        root = merge(a[0], merge(b[0], b[1]));
        return res;
    }

    void reverse(int l, int r) {
        Node[] a = split(root, l);
        Node[] b = split(a[1], r - l + 1);
        if (b[0] != null) b[0].rev ^= true;
        root = merge(a[0], merge(b[0], b[1]));
    }

    /* ---------- Public API ---------- */
    public int[] getResults(int[] nums, int[][] queries) {
        build(nums);
        List<Integer> ans = new ArrayList<>();
        for (int[] q : queries) {
            switch (q[0]) {
                case 1: update(q[1], q[2]);          break;
                case 2: ans.add(rangeXor(q[1], q[2])); break;
                case 3: reverse(q[1], q[2]);         break;
            }
        }
        return ans.stream().mapToInt(Integer::intValue).toArray();
    }
}
```

---

### 4.2 Python – Implicit Treap

```python
import sys, random
sys.setrecursionlimit(1 << 25)

class Node:
    __slots__ = ('val', 'xor', 'size', 'prio', 'rev', 'l', 'r')
    def __init__(self, val):
        self.val = val
        self.xor = val
        self.size = 1
        self.prio = random.randint(0, 1 << 30)
        self.rev = False
        self.l = None
        self.r = None

def sz(t): return t.size if t else 0
def xo(t): return t.xor  if t else 0

def push(t):
    if t and t.rev:
        t.rev = False
        t.l, t.r = t.r, t.l
        if t.l: t.l.rev ^= True
        if t.r: t.r.rev ^= True

def upd(t):
    if t:
        t.size = 1 + sz(t.l) + sz(t.r)
        t.xor  = t.val ^ xo(t.l) ^ xo(t.r)

def split(t, k):
    if not t: return (None, None)
    push(t)
    if k <= sz(t.l):
        l, r = split(t.l, k)
        t.l = r
        upd(t)
        return (l, t)
    else:
        l, r = split(t.r, k - sz(t.l) - 1)
        t.r = l
        upd(t)
        return (t, r)

def merge(a, b):
    if not a: return b
    if not b: return a
    push(a); push(b)
    if a.prio > b.prio:
        a.r = merge(a.r, b)
        upd(a)
        return a
    else:
        b.l = merge(a, b.l)
        upd(b)
        return b

class Solution:
    def __init__(self):
        self.root = None

    def build(self, arr):
        for v in arr:
            self.root = merge(self.root, Node(v))

    def point_update(self, idx, val):
        a, b = split(self.root, idx)
        mid, c = split(b, 1)
        mid.val = val
        upd(mid)
        self.root = merge(a, merge(mid, c))

    def range_xor(self, l, r):
        a, b = split(self.root, l)
        mid, c = split(b, r - l + 1)
        res = xo(mid)
        self.root = merge(a, merge(mid, c))
        return res

    def reverse(self, l, r):
        a, b = split(self.root, l)
        mid, c = split(b, r - l + 1)
        if mid: mid.rev ^= True
        self.root = merge(a, merge(mid, c))

    def getResults(self, nums, queries):
        self.build(nums)
        out = []
        for q in queries:
            if q[0] == 1:
                self.point_update(q[1], q[2])
            elif q[0] == 2:
                out.append(self.range_xor(q[1], q[2]))
            else:  # 3
                self.reverse(q[1], q[2])
        return out
```

---

### 4.3 C++ – Implicit Treap (Rope)

```cpp
#include <bits/stdc++.h>
using namespace std;

struct Node {
    int val, xorv, sz;
    bool rev;
    int prio;
    Node *l, *r;
    Node(int v) : val(v), xorv(v), sz(1), rev(false), prio(rand()), l(nullptr), r(nullptr) {}
};

int sz(Node* t)   { return t ? t->sz : 0; }
int xo(Node* t)   { return t ? t->xorv : 0; }

void push(Node* t) {
    if (!t || !t->rev) return;
    t->rev = false;
    swap(t->l, t->r);
    if (t->l) t->l->rev ^= true;
    if (t->r) t->r->rev ^= true;
}

void upd(Node* t) {
    if (!t) return;
    t->sz = 1 + sz(t->l) + sz(t->r);
    t->xorv = t->val ^ xo(t->l) ^ xo(t->r);
}

pair<Node*, Node*> split(Node* t, int k) {
    if (!t) return {nullptr, nullptr};
    push(t);
    if (k <= sz(t->l)) {
        auto res = split(t->l, k);
        t->l = res.second;
        upd(t);
        return {res.first, t};
    } else {
        auto res = split(t->r, k - sz(t->l) - 1);
        t->r = res.first;
        upd(t);
        return {t, res.second};
    }
}

Node* merge(Node* a, Node* b) {
    if (!a) return b;
    if (!b) return a;
    push(a); push(b);
    if (a->prio > b->prio) {
        a->r = merge(a->r, b);
        upd(a);
        return a;
    } else {
        b->l = merge(a, b->l);
        upd(b);
        return b;
    }
}

struct ImplicitTreap {
    Node* root = nullptr;
    void build(const vector<int>& a) {
        for (int v : a) root = merge(root, new Node(v));
    }

    void point_update(int idx, int val) {
        auto a = split(root, idx);
        auto b = split(a.second, 1);
        b.first->val = val;
        upd(b.first);
        root = merge(a.first, merge(b.first, b.second));
    }

    int range_xor(int l, int r) {
        auto a = split(root, l);
        auto b = split(a.second, r - l + 1);
        int ans = xo(b.first);
        root = merge(a.first, merge(b.first, b.second));
        return ans;
    }

    void reverse(int l, int r) {
        auto a = split(root, l);
        auto b = split(a.second, r - l + 1);
        if (b.first) b.first->rev ^= true;
        root = merge(a.first, merge(b.first, b.second));
    }
};

class Solution {
public:
    vector<int> getResults(vector<int>& nums, vector<vector<int>>& queries) {
        ImplicitTreap t;
        t.build(nums);
        vector<int> ans;
        for (auto& q : queries) {
            if (q[0] == 1) t.point_update(q[1], q[2]);
            else if (q[0] == 2) ans.push_back(t.range_xor(q[1], q[2]));
            else t.reverse(q[1], q[2]);
        }
        return ans;
    }
};
```

> **Note** – The C++ version uses `rand()` for simplicity; replace it with a fast PRNG (`mt19937`) for production code.

---

## 5. Blog Article – “**Range XOR & Reversal** – Good, Bad & Ugly in LeetCode 3526”

---

### 5.1 Why This Problem Pops Up  

LeetCode’s “Hard” problems rarely combine three seemingly trivial operations.  
They force you to **think in terms of segment operations** and to **augment trees** beyond the textbook `sum / min`.  
Interviewers love to see:

* Ability to **reduce complexity** from linear to logarithmic.  
* Understanding of *lazy propagation* for reversible segments.  
* Clear, maintainable code (even for a 10‑page implicit treap).

---

### 5.2 Good – What Works  

| ✅ | What’s great |
|----|--------------|
| **Implicit key** – no need to store explicit indices. |
| **Randomized balance** – very small code, fast average case. |
| **XOR augmentation** – trivial to maintain with `val ^ xor(left) ^ xor(right)`. |
| **Lazy reverse** – O(1) flag toggling, no per‑node swap. |
| **Reusable API** – `split`, `merge` can solve many segment problems (k‑th element, range sum, palindrome check, etc.). |

---

### 5.3 Bad – What Makes the Code Unpleasant  

| ❌ | Pain points |
|----|-------------|
| **Deep recursion** – `split/merge` are recursive; Python stack may overflow. |
| **Memory overhead** – each node stores 7 pointers/int/flags → ~80 bytes per element → ~8 MB for 10⁵ nodes – still fine but not trivial. |
| **Hard to debug** – off‑by‑one errors in split indices are common. |
| **Randomness** – deterministic test cases can expose unbalanced trees in rare edge cases. |

---

### 5.4 Ugly – Things You *really* want to avoid  

| 💀 | Avoid if possible |
|----|-------------------|
| **Manual balancing (AVL)** – extra code to maintain heights; not worth the marginal speed gain. |
| **Non‑lazy reversals** – swapping children inside a loop of length `O(r-l+1)` defeats the purpose. |
| **Wrong priority assignment** – using `rand()` without seeding (`srand`) can produce the same priority for all nodes → degeneracy. |
| **Pointer leaks** – forget to `delete` nodes (C++), you’ll run out of heap after many test cases. |

---

## 6. Closing Thoughts  

*LeetCode 3526* is a **classic “rope” problem**.  
The three operations map naturally to the `split/merge` skeleton; the trick is the **XOR + reverse flag**.  
If you can explain the design and implement it in any language with clean code, you’ll ace not only this problem but a whole suite of segment‑oriented interview questions.

---

### 6.1 Take‑away for Candidates  

1. **Start simple** – an implicit treap (randomized) is the sweet spot.  
2. **Test edge cases** – push to `n` elements with all operations on the whole array to ensure balance.  
3. **Use `__slots__` / `struct` packing** for memory.  
4. **Add assertions** (Python) or sanity checks (C++) to catch index bugs early.  

---

### 5.5 Call‑to‑Action  

Try the implementations above in your own LeetCode sandbox.  
Add a **debug mode** that prints node counts, depth, and flag statuses.  
Push the solution to **Codeforces 1226D** (the “D. Reversing Substrings”) to see how the same structure solves different segment problems.

--- 

### 6.6 Final Note  

When you master the “rope” pattern, the next time you see a problem that asks for “k‑th element, range sum, reverse sub‑array”, you’ll have a *ready‑to‑reuse* tool in your toolbox.  
LeetCode 3526 isn’t just a challenge; it’s a **mini‑masterclass in advanced data‑structures**. 

Happy coding! 🚀

---

## 6.7 Appendix – Quick Complexity Recap

| Operation | Time (worst) | Explanation |
|-----------|--------------|-------------|
| Point update | \(O(\log n)\) | Two splits + merge, flag updates propagate lazily. |
| Range XOR | \(O(\log n)\) | Two splits + merge, XOR constant-time. |
| Reverse | \(O(\log n)\) | Two splits + merge + flag toggle, no scanning. |

Memory: ~80 bytes × 10⁵ ≈ 8 MB.  
All within typical LeetCode limits.

---

## 7. Wrap‑Up  

You now have:

* **Full implementations** in **Python, C++**, and **Java**.  
* **A detailed explanation** of why the code works, where it falters, and how to **refactor** for production.  
* A **blog article** that frames the problem from an interview‑perspective, guiding you through the **good, bad, ugly** aspects of such advanced segment‑tree solutions.

Go ahead, submit your solution on LeetCode 3526, and let the implicit treap do its magic! 🎉

--- 

**Keywords:**  
`LeetCode 3526` | `range XOR` | `reverse subarray` | `implicit treap` | `rope` | `lazy propagation` | `logarithmic complexity` | `segment tree augmentation` | `interview data structure` | `advanced algorithmic problem`