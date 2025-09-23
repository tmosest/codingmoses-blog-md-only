---
title: LeetCode 1993. Operations on Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🎯 LeetCode 1993 – *Operations on Tree*  
> **A deep dive into the LockingTree data‑structure**  
> *How to ace the interview, write clean Java / Python / C++ code, and turn this problem into a resume‑boosting showcase.*

---

## 1. The Problem in a Nutshell

You’re given a rooted tree with `n` nodes (`0 … n-1`).  
`parent[i]` is the parent of node `i` (`parent[0] = -1`).  

Implement a class `LockingTree` that supports three operations:

| Operation | Preconditions | Result |
|-----------|---------------|--------|
| **lock(num, user)** | node `num` is unlocked | node `num` becomes locked by `user` |
| **unlock(num, user)** | node `num` is locked *by the same* `user` | node `num` becomes unlocked |
| **upgrade(num, user)** | node `num` is unlocked, it has a locked descendant, *and* no locked ancestor | lock `num` by `user`, unlock *all* its descendants |

All operations must return `true` if they succeed, otherwise `false`.

Constraints:  
- `2 ≤ n ≤ 2000`  
- At most 2000 total operations  
- `user` is a positive integer up to `10^4`

---

## 2. Why This Problem is a “Nice” Interview Question

| Aspect | Why It Matters |
|--------|----------------|
| **Tree Traversal** | Demonstrates knowledge of adjacency lists, DFS/BFS. |
| **State Management** | Shows how to keep mutable state (`locked[]`) across calls. |
| **Complexity Awareness** | Realizes that each operation can be `O(n)` in the worst case but is fine for the given constraints. |
| **API Design** | Highlights careful method signatures and return semantics. |

In an interview, being able to explain the trade‑offs, suggest optimisations (e.g., maintaining a locked‑descendant counter), and produce clean code is a huge plus.

---

## 3. The “Good” – A Straightforward, Readable Implementation

The core idea:

1. **Build children lists** from the `parent` array (O(n)).
2. Keep an array `locked[]` where `locked[i] = -1` means “unlocked” and otherwise stores the user id.
3. For **`upgrade`**:  
   - Check all ancestors (walk up `parent[]`) – O(height).  
   - DFS/BFS from `num` to unlock descendants and detect if any were locked.

Because `n ≤ 2000` and calls ≤ 2000, the simple O(n) per `upgrade` is perfectly acceptable.

Below are clean implementations in **Java, Python, and C++**.

---

## 4. Code – Java

```java
import java.util.*;

public class LockingTree {

    // ---------- Fields ----------
    private final int[] parent;          // parent relationship
    private final int[] locked;          // -1 = unlocked, else user id
    private final List<Integer>[] children; // adjacency list

    // ---------- Constructor ----------
    @SuppressWarnings("unchecked")
    public LockingTree(int[] parent) {
        this.parent = parent;
        int n = parent.length;
        this.locked = new int[n];
        Arrays.fill(locked, -1);          // all nodes start unlocked

        // build children lists
        this.children = new ArrayList[n];
        for (int i = 0; i < n; i++) children[i] = new ArrayList<>();
        for (int i = 1; i < n; i++) {
            children[parent[i]].add(i);
        }
    }

    // ---------- lock ----------
    public boolean lock(int num, int user) {
        if (locked[num] != -1) return false;   // already locked
        locked[num] = user;
        return true;
    }

    // ---------- unlock ----------
    public boolean unlock(int num, int user) {
        if (locked[num] == -1 || locked[num] != user) return false;
        locked[num] = -1;
        return true;
    }

    // ---------- upgrade ----------
    public boolean upgrade(int num, int user) {
        // 1. node must be unlocked
        if (locked[num] != -1) return false;

        // 2. no locked ancestor
        int cur = num;
        while (cur != -1) {
            if (locked[cur] != -1) return false; // locked ancestor found
            cur = parent[cur];
        }

        // 3. DFS/BFS to unlock descendants and check if any were locked
        boolean hasLockedDescendant = false;
        Deque<Integer> stack = new ArrayDeque<>();
        stack.push(num);

        while (!stack.isEmpty()) {
            int node = stack.pop();
            for (int child : children[node]) {
                if (locked[child] != -1) {
                    hasLockedDescendant = true;
                }
                // unlock descendant if it was locked
                locked[child] = -1;
                stack.push(child);
            }
        }

        if (!hasLockedDescendant) return false; // rule 2 violated

        // lock the node itself
        locked[num] = user;
        return true;
    }
}
```

**Why it’s good**

- **Clarity** – One helper structure (`children`) holds the tree.
- **Correctness** – All three conditions for `upgrade` are explicitly checked.
- **Performance** – Only linear passes are needed; `O(n)` per `upgrade` fits the constraints.

---

## 5. Code – Python

```python
class LockingTree:
    def __init__(self, parent):
        self.parent = parent
        n = len(parent)
        self.locked = [-1] * n            # -1 = unlocked, else user id
        self.children = [[] for _ in range(n)]
        for i in range(1, n):
            self.children[parent[i]].append(i)

    def lock(self, num: int, user: int) -> bool:
        if self.locked[num] != -1:
            return False
        self.locked[num] = user
        return True

    def unlock(self, num: int, user: int) -> bool:
        if self.locked[num] == -1 or self.locked[num] != user:
            return False
        self.locked[num] = -1
        return True

    def upgrade(self, num: int, user: int) -> bool:
        # 1. node must be unlocked
        if self.locked[num] != -1:
            return False

        # 2. no locked ancestor
        cur = num
        while cur != -1:
            if self.locked[cur] != -1:
                return False
            cur = self.parent[cur]

        # 3. DFS to unlock descendants, detect any locked
        stack = [num]
        has_locked_descendant = False
        while stack:
            node = stack.pop()
            for child in self.children[node]:
                if self.locked[child] != -1:
                    has_locked_descendant = True
                self.locked[child] = -1      # unlock
                stack.append(child)

        if not has_locked_descendant:
            return False

        # lock the node itself
        self.locked[num] = user
        return True
```

Python’s implementation follows the same logic as Java but takes advantage of **dynamic lists** and a **clear DFS stack**.

---

## 6. Code – C++

```cpp
#include <vector>
#include <stack>

class LockingTree {
public:
    LockingTree(const std::vector<int>& parent)
        : parent(parent), n(parent.size()), locked(n, -1) {
        children.resize(n);
        for (int i = 1; i < n; ++i)
            children[parent[i]].push_back(i);
    }

    bool lock(int num, int user) {
        if (locked[num] != -1) return false;
        locked[num] = user;
        return true;
    }

    bool unlock(int num, int user) {
        if (locked[num] == -1 || locked[num] != user) return false;
        locked[num] = -1;
        return true;
    }

    bool upgrade(int num, int user) {
        if (locked[num] != -1) return false;   // must be unlocked

        // check ancestors
        int cur = num;
        while (cur != -1) {
            if (locked[cur] != -1) return false; // locked ancestor
            cur = parent[cur];
        }

        // DFS/BFS over descendants
        bool hasLocked = false;
        std::stack<int> st;
        st.push(num);
        while (!st.empty()) {
            int node = st.top(); st.pop();
            for (int child : children[node]) {
                if (locked[child] != -1) hasLocked = true;
                locked[child] = -1;           // unlock
                st.push(child);
            }
        }

        if (!hasLocked) return false; // no locked descendant

        locked[num] = user;   // lock the node itself
        return true;
    }

private:
    const std::vector<int>& parent;
    std::vector<int> locked;
    std::vector<std::vector<int>> children;
    int n;
};
```

**Why it’s good**

- Uses `std::vector` for everything – no dynamic allocation overhead.
- Explicitly keeps the tree shape (`children`).
- Uses `std::stack` for DFS; no recursion depth concerns.

---

## 6. The “Bad” – A Less‑Than‑Ideal Approach

Some candidates try to rebuild the tree on every `upgrade`, or maintain a global lock map that is **not** indexed by node id.  
This can lead to:

- **Higher memory usage** (maps vs arrays).
- **Slower ancestor checks** (scanning the whole map each time).
- **Harder to reason about** (mixed data structures).

**Bottom line** – if you see yourself in this “bad” code, it’s time to refactor.

---

## 7. The “Ugly” – Trying to Over‑Optimize Prematurely

A typical “ugly” attempt:

- **Pre‑compute a counter of locked descendants** for every node, update it on every `lock`/`unlock`.  
  While it reduces `upgrade` cost to `O(height)` + `O(1)` for descendant‑checking, the implementation becomes **bug‑prone**:
  - You must carefully propagate counter changes up the tree.
  - A single forgotten update can corrupt all subsequent answers.

- **Recursive DFS without a stack** on Python could hit Python’s recursion depth limit even if `n ≤ 2000`.  

If you need to show such an optimisation, **document it clearly** and provide fallback logic for correctness.

---

## 8. Optimisation Ideas (for “nice” interviewers)

| Idea | When to Use | Complexity |
|------|--------------|------------|
| **Locked‑descendant counter** (`descLocks[]`) | If you want to guarantee `O(height)` for every `upgrade` | `O(height)` for lock/unlock, `O(height)` for upgrade |
| **Euler tour + segment tree** | For larger constraints (e.g., `n = 10^5`) | `O(log n)` per operation |
| **Bitset for users** | If `user` IDs are small | Constant‑time check if any ancestor is locked |

Because the constraints here are tiny, the “good” straightforward implementation is usually the best choice – you save time and avoid bugs.

---

## 9. Turning This Problem into a *Resume‑Boosting Story*

> **“I solved LeetCode 1993 – Operations on Tree – in Java, Python, and C++ with a clean API, documented code, and a clear complexity analysis.”**

In your interview or cover letter, mention:

1. **Tree‑based state machine** – I built an adjacency list and maintained a lock array.  
2. **Clear pre‑condition checks** – Every method returns a boolean and never mutates state when the pre‑conditions fail.  
3. **Scalable design** – While my base solution is `O(n)` per upgrade, I also sketched an optional O(1) locked‑descendant counter for production‑grade systems.  

Adding the *blog* (this post) to your portfolio or LinkedIn gives recruiters a concrete, well‑commented sample they can run immediately.

---

## 10. Final Checklist for the Interview

- ✅ **Explain** the data‑structure in words first.
- ✅ Write **clean, typed code** (Java + annotations, Python 3.8+ type hints, C++14+).
- ✅ **Walk through** a sample `upgrade` call, illustrating each pre‑condition.
- ✅ Mention a possible *optimization* and discuss when it is worth it.

Good luck – you’re now ready to show recruiters you can **design, reason, and code** a non‑trivial tree problem in three major languages! 🚀  

---  

### 📚 Further Reading

- “Tree Traversals – DFS vs BFS” – a quick refresher on adjacency lists.  
- “State‑ful Data‑Structures in Interviews” – why arrays are often the best choice.  
- “Java Generics vs Python Dynamic Typing” – choosing the right tool for the job.  

Happy coding!