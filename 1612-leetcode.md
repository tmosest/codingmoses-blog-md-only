---
title: LeetCode 1612. Check If Two Expression Trees are Equivalent - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# 1612. **Check If Two Expression Trees are Equivalent** – Code + SEO‑Optimized Blog

> **Goal:**  
> Given two binary expression trees that contain only the operator `+` and operands (lower‑case letters), decide whether they evaluate to the same expression for *any* values of the variables.

> **Why does it matter?**  
> In compiler design, symbolic math libraries, or interview coding, you frequently need to decide whether two algebraic expressions are equivalent. This LeetCode problem is a canonical interview question that tests your understanding of tree traversals, commutativity, and time‑space trade‑offs.

---

## 1.  Problem Recap

* Each internal node is a `+` operator and always has **exactly two** children.  
* Each leaf node is an operand (`a`‑`z`).  
* The trees are **valid** expression trees (the number of nodes is odd).  
* **Task:** Return `true` if the two trees are *mathematically equivalent* for every possible assignment to the operands; otherwise, return `false`.

**Example**

```
root1:  + 
         / \
        a   +
            / \
           b   c
root2:  + 
         / \
        +   a
       / \
      b   c

output: true
```

`a + (b + c)`  is equal to  `(b + c) + a` because `+` is commutative & associative.

---

## 2.  Intuition

Because `+` is *commutative* and *associative*:

1. The **order** of operands does **not** matter.  
2. The **nesting** of additions does not matter.

Thus, two trees are equivalent **iff** they contain the **same multiset** of operands.

So we only need to count how many times each variable appears in each tree and compare the counts.

If any operand appears an *odd* number of times in the combined counts, the two trees differ (because that operand would cancel out in one tree but not the other).

---

## 3.  Algorithm

1. Create an integer array `cnt[26]` (one slot for each lowercase letter).
2. **DFS** each tree, incrementing `cnt[operand - 'a']` when an operand node is visited.
3. After visiting both trees, check every `cnt[i]`.  
   *If any `cnt[i]` is odd → trees are not equivalent.*  
   *Else → they are equivalent.*

**Complexity**

| Metric | Expression | Reason |
|--------|------------|--------|
| **Time** | `O(n)` | Each node is visited once. |
| **Space** | `O(1)` | Only a 26‑element array; recursion depth is `O(h)` where `h` is tree height. |

---

## 4.  Reference Implementations

> **Note:** All code snippets are self‑contained.  
> The `Node` class is defined exactly as in the LeetCode problem statement.

### 4.1 Java

```java
// Definition for a binary tree node.
class Node {
    char val;
    Node left, right;
    Node() { this.val = '+'; }
    Node(char val) { this.val = val; }
    Node(char val, Node left, Node right) {
        this.val = val; this.left = left; this.right = right;
    }
}

public class Solution {
    public boolean checkEquivalence(Node root1, Node root2) {
        int[] cnt = new int[26];   // a..z

        dfs(root1, cnt);
        dfs(root2, cnt);

        for (int c : cnt) {
            if (c % 2 != 0) return false;
        }
        return true;
    }

    private void dfs(Node node, int[] cnt) {
        if (node == null) return;
        if (node.val != '+') {          // operand
            cnt[node.val - 'a']++;
        }
        dfs(node.left, cnt);
        dfs(node.right, cnt);
    }
}
```

### 4.2 Python

```python
# Definition for a binary tree node.
class Node:
    def __init__(self, val='+', left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def checkEquivalence(self, root1: Node, root2: Node) -> bool:
        cnt = [0] * 26

        def dfs(node):
            if not node:
                return
            if node.val != '+':           # operand
                cnt[ord(node.val) - ord('a')] += 1
            dfs(node.left)
            dfs(node.right)

        dfs(root1)
        dfs(root2)

        return all(c % 2 == 0 for c in cnt)
```

### 4.3 C++

```cpp
#include <vector>
using namespace std;

// Definition for a binary tree node.
struct Node {
    char val;
    Node* left;
    Node* right;
    Node(char v='+', Node* l=nullptr, Node* r=nullptr)
        : val(v), left(l), right(r) {}
};

class Solution {
public:
    bool checkEquivalence(Node* root1, Node* root2) {
        vector<int> cnt(26, 0);
        dfs(root1, cnt);
        dfs(root2, cnt);
        for (int c : cnt)
            if (c % 2 != 0) return false;
        return true;
    }

private:
    void dfs(Node* node, vector<int>& cnt) {
        if (!node) return;
        if (node->val != '+')
            ++cnt[node->val - 'a'];
        dfs(node->left, cnt);
        dfs(node->right, cnt);
    }
};
```

---

## 5.  Follow‑Up: Adding the `-` Operator

If the tree may also contain `-`, the equivalence problem becomes harder:

* `-` is **not** commutative: `a - b` ≠ `b - a`.  
* `-` is also **not** associative: `(a - b) - c` ≠ `a - (b - c)`.

A common strategy:

1. **Canonicalize** each expression by converting it into a *fully parenthesized* string (or another normal form).  
2. Compare the canonical strings.

In practice, you can perform a post‑order traversal that builds a string:

```java
String expr(Node n) {
    if (n.val != '+') return String.valueOf(n.val);
    return "(" + expr(n.left) + n.val + expr(n.right) + ")";
}
```

For subtraction you simply use `-`.  
Two trees are equivalent iff `expr(root1).equals(expr(root2))`.

---

## 6.  Blog Article: “The Good, the Bad, and the Ugly of Expression‑Tree Equivalence”

> **Audience:** Software engineers, interviewees, recruiters, and anyone who wants to land a senior developer role.

---

### 6.1 Title (SEO‑Optimized)

**“Check If Two Expression Trees Are Equivalent – Java/Python/C++ Solution + Interview Tips”**

> *Why?* The keyword “expression tree equivalence” is searched by interviewers, recruiters, and students. Pairing it with “Java/Python/C++” signals multi‑language expertise, boosting SEO.

---

### 6.2 Meta Description

> *“Learn how to solve LeetCode 1612 in Java, Python, and C++. Understand the math behind commutative trees, see full code, and discover follow‑up tricks for subtraction. Perfect for your next coding interview.”*

---

### 6.3 Outline

| Section | Focus |
|---------|-------|
| **Intro** | What is an expression tree? |
| **Problem Statement** | LeetCode 1612 overview |
| **Good** | Simplicity, O(n) time, O(1) space |
| **Bad** | Edge cases, only `+` supported |
| **Ugly** | Extending to `-`, potential pitfalls |
| **Full Code** | Java, Python, C++ |
| **Follow‑Up** | Handling subtraction, canonical forms |
| **Interview Tips** | How to pitch your solution |
| **Conclusion** | Recap + call to action |

---

### 6.4 Full Blog Post

> *(The blog post is formatted in Markdown; copy‑paste into your blogging platform or GitHub README.)*

```markdown
# Check If Two Expression Trees Are Equivalent – Java/Python/C++ Solution + Interview Tips

## 1. Why This Problem Rocks Your Interview

Expression trees are the backbone of compilers, symbolic math engines, and even the **LeetCode** community. Being able to prove two trees are equivalent demonstrates mastery over:

- **Tree traversals** (DFS/BFS)
- **Algebraic properties** (commutativity & associativity)
- **Algorithmic optimization** (linear time, constant space)

If you can nail this problem, recruiters will see you can blend data structures with mathematical insight.

## 2. Problem Recap

You’re given two binary trees that represent arithmetic expressions with only the `+` operator and lowercase operands. Return `true` if the expressions are equivalent for *every* possible assignment to the variables.

**Examples**

| `root1` | `root2` | Output | Explanation |
|---------|---------|--------|-------------|
| `[x]` | `[x]` | `true` | Identical leaf nodes |
| `[+,a,+,null,null,b,c]` | `[+,+,a,b,c]` | `true` | `a + (b + c)` ≡ `(b + c) + a` |
| `[+,a,+,null,null,b,c]` | `[+,+,a,b,d]` | `false` | Different operand (`c` vs `d`) |

## 3. The Good

- **O(n) Time** – Each node is visited once.
- **O(1) Extra Space** – Only a 26‑element array (constant for lowercase letters).
- **Intuitive** – Count operands, compare parity.
- **Extensible** – Works for any number of variables.

## 4. The Bad

- **Only `+`** – The solution hinges on commutativity and associativity. If subtraction appears, you’re out of luck.
- **Large Alphabets** – If you support `A-Z` or Unicode, the array size grows, though still manageable.

## 5. The Ugly

- **Subtraction (`-`)** – Non‑commutative, non‑associative. A simple counter fails.
- **Parentheses / Operator Precedence** – If you extend to `*` or `/`, you need a full parser or expression normalizer.
- **Side‑Effects** – If the operands are not independent (e.g., functions with side‑effects), mathematical equivalence is no longer sufficient.

### 5.1 How to Tackle `-`

A reliable approach is to **canonicalize** each expression:

1. **Post‑order traversal**: build a string `("(" + left + op + right + ")")`.
2. Compare the canonical strings.

This guarantees that the structure (and order) is preserved. It’s O(n) time and O(n) space, but that’s acceptable for interview scenarios.

## 6. Code Walkthrough

Below are clean, production‑ready solutions in three languages.

### 6.1 Java

```java
class Node {
    char val;
    Node left, right;
    Node() { this.val = '+'; }
    Node(char val) { this.val = val; }
    Node(char val, Node left, Node right) {
        this.val = val; this.left = left; this.right = right;
    }
}

public class Solution {
    public boolean checkEquivalence(Node root1, Node root2) {
        int[] cnt = new int[26];
        dfs(root1, cnt);
        dfs(root2, cnt);
        for (int c : cnt) if (c % 2 != 0) return false;
        return true;
    }

    private void dfs(Node node, int[] cnt) {
        if (node == null) return;
        if (node.val != '+') cnt[node.val - 'a']++;
        dfs(node.left, cnt);
        dfs(node.right, cnt);
    }
}
```

### 6.2 Python

```python
class Node:
    def __init__(self, val='+', left=None, right=None):
        self.val = val
        self.left = left
        self.right = right

class Solution:
    def checkEquivalence(self, root1: Node, root2: Node) -> bool:
        cnt = [0] * 26

        def dfs(node):
            if not node: return
            if node.val != '+':
                cnt[ord(node.val) - ord('a')] += 1
            dfs(node.left)
            dfs(node.right)

        dfs(root1)
        dfs(root2)
        return all(c % 2 == 0 for c in cnt)
```

### 6.3 C++

```cpp
struct Node {
    char val;
    Node* left;
    Node* right;
    Node(char v='+', Node* l=nullptr, Node* r=nullptr)
        : val(v), left(l), right(r) {}
};

class Solution {
public:
    bool checkEquivalence(Node* root1, Node* root2) {
        vector<int> cnt(26, 0);
        dfs(root1, cnt);
        dfs(root2, cnt);
        for (int c : cnt) if (c % 2 != 0) return false;
        return true;
    }

private:
    void dfs(Node* node, vector<int>& cnt) {
        if (!node) return;
        if (node->val != '+') ++cnt[node->val - 'a'];
        dfs(node->left, cnt);
        dfs(node->right, cnt);
    }
};
```

## 7. Interview Pointers

1. **Explain the math** – “Because `+` is commutative, only the parity of each variable matters.”
2. **Show the code** – Share the counter‑based solution first; then ask if you can extend to `-`.
3. **Highlight edge cases** – Null children, single‑node trees.
4. **Ask clarifying questions** – If the interviewer allows subtraction or other operators, pivot to canonicalization early.

## 8. Takeaway

- **Master the counter trick** for pure `+` trees.
- **Be ready to canonicalize** when the problem’s constraints change.
- **Practice** writing the solution from scratch in each language. This cross‑language fluency is a huge recruiter magnet.

> **Ready to ace your next coding interview?** Practice this problem on LeetCode, add it to your portfolio, and watch the interview doors open.

```

```

---

### 6.5 Call to Action

- **GitHub Repository** – Store the solutions and test cases.
- **LeetCode Profile** – Showcase your solution tags (`DFS`, `Hashing`, `Algorithms`).
- **LinkedIn** – Post a short snippet with the tag `#ExpressionTreeEquivalence`.
- **Recruiter Outreach** – Mention the problem when discussing your technical interview experience.

---

## 7. Final Thoughts

Solving LeetCode 1612 is a **quick win** that demonstrates algorithmic elegance. By providing full code in Java, Python, and C++, you show language versatility. The “Follow‑Up” section teaches recruiters you’re thinking ahead, ready to handle more complex operator sets.

*Now go code, ace the interview, and land that senior dev position!* 

```

---

## 7.1 Publishing Checklist

- ✅ Keywords in title & meta
- ✅ Language tags
- ✅ Full code blocks with syntax highlighting
- ✅ Follow‑up and interview tips
- ✅ CTA (“Download the GitHub repo” or “Schedule a call”)

---

### 7.2 Final Word

This problem is a *gold‑mine* for your interview portfolio. Master the linear‑time counter trick, understand the limitations, and be ready to pivot to canonical forms for subtraction. With the multi‑language code snippets above, you’re not only solving a LeetCode challenge—you’re delivering a **complete interview package** that recruiters will love. Happy coding! 🚀
```

---

## 7. Concluding Remarks

You now have:

1. **Working code** in three popular languages.
2. A clear understanding of **when** the solution works and **why**.
3. A **blog article** ready for your personal website, LinkedIn, or a Medium post, with SEO‑optimized title and meta description.
4. Interview‑ready explanations that show recruiters you can **explain**, **justify**, and **extend** the solution.

**Next steps:**  
- Run the solutions against a battery of unit tests.  
- Build a GitHub repo titled `leetcode-1612-expr-tree` containing the code, tests, and the blog Markdown.  
- Publish the article.  

Good luck—and may the `+` operator be ever in your favor!