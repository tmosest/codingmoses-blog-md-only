---
title: LeetCode 255. Verify Preorder Sequence in Binary Search Tree - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 🔎 LeetCode 255 – Verify Preorder Sequence in Binary Search Tree  
### “Good, Bad, & Ugly” – A Job‑Interview Friendly Deep‑Dive

> **Keywords** – LeetCode 255, Verify Preorder Sequence, Binary Search Tree, O(n) solution, constant space, interview prep, coding interview, algorithm, Java, Python, C++.

---

### 1️⃣ Problem Overview

You’re given an array `preorder` of **unique** integers.  
`preorder` is claimed to be a **pre‑order traversal** of a Binary Search Tree (BST).  
Return `true` if the sequence is valid, otherwise `false`.

| Example | Input | Output | Why |
|---------|-------|--------|-----|
| 1 | `[5,2,1,3,6]` | `true` | Valid BST: `5` → left `2` → right `6` |
| 2 | `[5,2,6,1,3]` | `false` | `1` appears after entering the right subtree of `2` → violates BST property |

**Constraints**

- `1 <= preorder.length <= 10⁴`
- `1 <= preorder[i] <= 10⁴`
- All values are distinct

**Follow‑up:** Can you solve it using **O(1)** extra space?

---

### 2️⃣ Core Insight

A preorder traversal visits **root → left → right**.  
When walking the array:

- While the next value is **smaller** than the last pushed node, you’re still inside its **left subtree** – just push it.
- When the next value becomes **larger**, you’re stepping **into a right subtree**. All nodes popped so far are ancestors whose right subtree you’re now visiting.  
  The *last popped value* becomes a **lower bound**: any future node must be **greater** than this bound.

This simple stack simulation yields an **O(n)** time algorithm and an **O(n)** space stack.  
The follow‑up trick is to reuse the input array as the stack to bring the space down to **O(1)**.

---

### 3️⃣ Algorithms & Code

#### 3.1 Java – O(n) with a Stack

```java
class Solution {
    public boolean verifyPreorder(int[] preorder) {
        int lowerBound = Integer.MIN_VALUE;
        Deque<Integer> stack = new ArrayDeque<>();

        for (int value : preorder) {
            if (value < lowerBound) return false;          // violates BST

            // Moving to a right subtree: pop all ancestors smaller than current
            while (!stack.isEmpty() && value > stack.peek()) {
                lowerBound = stack.pop();                  // update bound
            }

            stack.push(value);                            // stay in left subtree
        }
        return true;
    }
}
```

#### 3.2 Java – O(n) **O(1)** Extra Space (reuse input array)

```java
class Solution {
    public boolean verifyPreorder(int[] preorder) {
        int lowerBound = Integer.MIN_VALUE;
        int i = -1;                                      // index of top of simulated stack

        for (int val : preorder) {
            if (val < lowerBound) return false;

            while (i >= 0 && val > preorder[i]) {        // pop while we go right
                lowerBound = preorder[i--];
            }
            preorder[++i] = val;                        // push onto “stack”
        }
        return true;
    }
}
```

#### 3.3 Python – O(n) with a List as Stack

```python
class Solution:
    def verifyPreorder(self, preorder: List[int]) -> bool:
        lower = float('-inf')
        stack = []

        for v in preorder:
            if v < lower:
                return False
            while stack and v > stack[-1]:
                lower = stack.pop()
            stack.append(v)
        return True
```

#### 3.4 C++ – O(n) with a `vector` as Stack

```cpp
class Solution {
public:
    bool verifyPreorder(vector<int>& preorder) {
        int lower = INT_MIN;
        vector<int> stack;           // acts like a stack

        for (int v : preorder) {
            if (v < lower) return false;
            while (!stack.empty() && v > stack.back()) {
                lower = stack.back();
                stack.pop_back();
            }
            stack.push_back(v);
        }
        return true;
    }
};
```

---

### 4️⃣ Complexity Analysis

| Variant | Time | Extra Space |
|---------|------|-------------|
| Java Stack | **O(n)** | **O(n)** |
| Java O(1) | **O(n)** | **O(1)** |
| Python | **O(n)** | **O(n)** |
| C++ | **O(n)** | **O(n)** |

- **Worst‑case stack size**: `n` (e.g., sorted ascending array → every node is a left child).
- **Best‑case**: Constant stack growth for a balanced tree.

---

### 5️⃣ Good, Bad, & Ugly

| **Aspect** | **Good** | **Bad** | **Ugly** |
|------------|----------|---------|----------|
| **Conceptual Simplicity** | Uses the natural “stack of ancestors” idea – easy to reason. | Must remember the “lower bound” trick; a small oversight leads to a wrong answer. | Implementing the O(1) trick requires careful index handling; a mistake can corrupt the input array. |
| **Performance** | Linear time; constant additional memory for the optimal solution. | None. | None. |
| **Readability** | Stack version is self‑explanatory. | O(1) version less obvious to beginners; comments are vital. | None. |
| **Testing Edge Cases** | Edge cases: empty array, single element, strictly increasing or decreasing sequences. | All values unique by constraint, so duplicates are not a worry. | If input length = 1, the stack logic still works. |
| **Production Use** | For interview or coding challenges, stack version is fine. | In memory‑critical environments, use O(1) version. | None. |

---

### 6️⃣ Follow‑Up – Why `O(1)` Extra Space Works

The key is that we never need the original array after it’s read.  
By treating the first `k` elements of `preorder` as a stack, we avoid allocating a separate container.  
The algorithm remains logically the same; only the data structure is in‑place.

---

### 7️⃣ Conclusion & Career Take‑away

- **Interview Tip:** Explain the stack logic first; it shows you understand BST properties.  
- **Job‑Ready Skill:** Mastering in‑place algorithms and space optimization demonstrates depth.  
- **Practical Use:** Similar stack‑based checks appear in XML/HTML parsers, expression evaluators, and syntax validators.

> **Remember:** “Good” in an interview is *clarity*; “bad” is *implementation detail*; “ugly” is *error‑prone logic*.  
> If you can walk through this solution in under 5 minutes and explain the O(1) trick, you’ll impress any hiring manager!

---

#### 📚 Further Reading

- **LeetCode 100 – Same algorithm with a lower bound.**  
- **Binary Search Tree – Pre‑order vs In‑order vs Post‑order.**  
- **Space‑Complexity Optimization – In‑place algorithms.**

Happy coding, and good luck landing that dream job! 🚀

---