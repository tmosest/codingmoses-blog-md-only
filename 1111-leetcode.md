---
title: LeetCode 1111. Maximum Nesting Depth of Two Valid Parentheses Strings - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Problem Recap – 1111. Maximum Nesting Depth of Two Valid Parentheses Strings  

| What | How |
|------|-----|
| **Input** | `seq` – a valid parentheses string (VPS).  Length ≤ 10 000. |
| **Goal** | Split `seq` into two disjoint subsequences `A` and `B` that are also VPS.  The **maximum** of `depth(A)` and `depth(B)` must be **minimal**. |
| **Output** | An integer array `answer` of length `|seq|` where `answer[i] = 0` if `seq[i]` belongs to `A`, otherwise `answer[i] = 1`. |

> **Why does this problem matter?**  
> It teaches you how to use a *single counter* (or stack) to distribute parentheses between two strings in a way that balances their depths. This is a classic LeetCode “medium” that frequently appears in interviews because it tests both understanding of stack‑based depth and greedy thinking.

---

## 2. The Greedy Insight (the “Good” part)

If we walk through the string from left to right, we can keep a single counter `depth` that represents the current nesting level of the original string.

- When we see `'('`, we *have to* open a bracket in either `A` or `B`.  
  We assign it to **one of the two groups** and then **increment** `depth`.  
  The trick is to alternate the group assignment between consecutive `'('` at the same nesting level.  
  This is achieved by taking `depth % 2`.

- When we see `')'`, the matching `'('` has already been assigned to a group.  
  To keep the parentheses balanced we must put this `')'` into the **same** group as its opening bracket.  
  Therefore we decrement `depth` *first* (since the depth of the matching pair is finished) and then use `depth % 2` to decide the group.

> **Why does this work?**  
> The parity of the current depth tells us whether the current pair of parentheses is at an even or odd level of nesting. By flipping the parity each time we open a bracket, we spread the nesting evenly across the two groups, ensuring that the **maximum depth of either group is `ceil(originalDepth/2)`** – the theoretical minimum.

---

## 3. The “Bad” – Edge Cases & Pitfalls

| Pitfall | Fix |
|---------|-----|
| **Using the depth after incrementing for `'('`** | Increment *after* deciding the group, otherwise you would be using the *next* depth level. |
| **Using the depth before decrementing for `')'`** | Decrement *before* deciding the group, because the closing parenthesis belongs to the same pair that ended at this depth. |
| **Assuming a stack is needed** | It isn’t – a simple integer counter suffices. Using a stack adds unnecessary space and time. |
| **Confusing “subsequence” with “substring”** | The assignment array does not reorder characters; it merely flags belonging. No re‑arrangement is performed. |

---

## 4. The “Ugly” – Performance & Readability

- **Time Complexity**: `O(n)` – one pass over the string.  
- **Space Complexity**: `O(n)` for the answer array (required by the problem).  
- **Readability**: The counter‑based solution is short and clear, but the `% 2` trick can be confusing at first glance. Adding comments or a small helper function (`group = depth & 1`) helps.

---

## 5. Reference Implementations

Below you’ll find a **clean, production‑ready** solution in **Java**, **Python**, and **C++**. All three use the same counter‑based logic.

---

### 5.1 Java

```java
/**
 * 1111. Maximum Nesting Depth of Two Valid Parentheses Strings
 * Greedy O(n) solution that uses a single depth counter.
 */
public class Solution {
    public int[] maxDepthAfterSplit(String seq) {
        int n = seq.length();
        int[] answer = new int[n];
        int depth = 0;               // current nesting depth

        for (int i = 0; i < n; i++) {
            char c = seq.charAt(i);
            if (c == '(') {
                // Assign based on current depth, then increment
                answer[i] = depth % 2;
                depth++;
            } else { // c == ')'
                // Decrement first, then assign
                depth--;
                answer[i] = depth % 2;
            }
        }
        return answer;
    }
}
```

---

### 5.2 Python

```python
class Solution:
    def maxDepthAfterSplit(self, seq: str) -> List[int]:
        depth = 0
        answer = []

        for ch in seq:
            if ch == '(':
                answer.append(depth % 2)
                depth += 1
            else:           # ch == ')'
                depth -= 1
                answer.append(depth % 2)

        return answer
```

---

### 5.3 C++

```cpp
class Solution {
public:
    vector<int> maxDepthAfterSplit(string seq) {
        vector<int> answer;
        answer.reserve(seq.size());
        int depth = 0;                     // current nesting depth

        for (char c : seq) {
            if (c == '(') {
                answer.push_back(depth & 1);   // same as depth % 2
                ++depth;
            } else {                           // c == ')'
                --depth;
                answer.push_back(depth & 1);
            }
        }
        return answer;
    }
};
```

> **Why `depth & 1` instead of `% 2`?**  
> Bit‑wise AND is a tiny performance win (and also demonstrates the parity trick). It’s fully equivalent for non‑negative integers.

---

## 6. Blog Article – “The Good, The Bad, and The Ugly of LeetCode 1111”

> **Title**: *Mastering LeetCode 1111: A Deep Dive into the “Maximum Nesting Depth” Puzzle*  
> **Meta Description**: Learn the optimal O(n) solution for LeetCode 1111 in Java, Python, and C++. Understand the greedy strategy, pitfalls, and interview‑ready insights.

---

### 6.1 Introduction  

If you’re hunting a software‑engineering role, you’ve probably faced a string‑parsing interview question that looks simple at first glance but hides a clever trick. LeetCode 1111 – *Maximum Nesting Depth of Two Valid Parentheses Strings* – is one such gem. It tests not only your coding chops but also your ability to think about *balance* and *distribution* in a single pass.

In this article we’ll break down:

1. The **core idea** (the good).  
2. Common **missteps** (the bad).  
3. Performance quirks and readability tips (the ugly).  
4. Full, cross‑language implementations.

Ready? Let’s dive.

---

### 6.2 Problem Restatement  

You’re given a string `seq` that is a valid parentheses string (VPS). You must split it into two disjoint subsequences `A` and `B` such that both are VPS. Your goal: **minimize** `max(depth(A), depth(B))`. Return an array that tells which group each character goes to (`0` for `A`, `1` for `B`).

---

### 6.3 The Greedy Insight – The Good  

Think of the string as a series of *depth levels*:

```
   0   1   2   3   2   1   0
  (()()) → depth increases and decreases
```

If we always split brackets at **alternating depths**, the deepest pair will be shared as evenly as possible. Formally:

- For an opening `'('` at depth `d`, put it in group `d % 2`.
- For the matching closing `')'`, after decreasing the depth, put it in group `d % 2` as well.

The beauty? One integer counter (`depth`) suffices; no stack or hash map needed. The maximum depth of either group becomes `ceil(originalDepth / 2)`, which is the theoretical minimum.

---

### 6.4 The Pitfalls – The Bad  

| Mistake | Consequence | Fix |
|---------|-------------|-----|
| Increment `depth` *before* assigning `'('` | Uses wrong parity for the current opening | Assign first, then `depth++` |
| Decrement `depth` *after* assigning `')'` | Closing bracket gets wrong group | Decrement first, then assign |
| Assuming a stack is required | Extra memory, slower code | Use a counter |
| Misreading “subsequence” as “substring” | Wrong answer if you reorder | Keep original order; only flag membership |

---

### 6.5 Performance & Readability – The Ugly  

- **Time**: O(n) – one linear pass.  
- **Space**: O(n) – answer array (must be returned).  
- **Readability**: `% 2` or `& 1` can be confusing. Adding a helper (`int group = depth & 1;`) or comments clarifies intent.

---

### 6.6 Full Code Samples  

> All three implementations follow the same algorithm.  
> Choose the language that matches your job interview stack.

#### Java

```java
public class Solution {
    public int[] maxDepthAfterSplit(String seq) {
        int n = seq.length();
        int[] answer = new int[n];
        int depth = 0;

        for (int i = 0; i < n; i++) {
            char c = seq.charAt(i);
            if (c == '(') {
                answer[i] = depth % 2;
                depth++;
            } else {
                depth--;
                answer[i] = depth % 2;
            }
        }
        return answer;
    }
}
```

#### Python

```python
class Solution:
    def maxDepthAfterSplit(self, seq: str) -> List[int]:
        depth = 0
        ans = []

        for ch in seq:
            if ch == '(':
                ans.append(depth % 2)
                depth += 1
            else:          # ')'
                depth -= 1
                ans.append(depth % 2)
        return ans
```

#### C++

```cpp
class Solution {
public:
    vector<int> maxDepthAfterSplit(string seq) {
        vector<int> ans;
        ans.reserve(seq.size());
        int depth = 0;

        for (char c : seq) {
            if (c == '(') {
                ans.push_back(depth & 1);
                ++depth;
            } else {           // ')'
                --depth;
                ans.push_back(depth & 1);
            }
        }
        return ans;
    }
};
```

---

### 6.7 Take‑Away for the Interviewer  

- **Why this is interview‑ready**: The solution demonstrates O(n) time, O(1) auxiliary space, and a clean greedy approach.  
- **Why it matters for hiring**: You’ll be able to explain *why* parity of depth works, anticipate pitfalls, and write concise code.  
- **What to practice next**: Try the “Split Array with Maximum Sum” problem; it also uses greedy partitioning.  

---

### 6.8 Final Thoughts  

LeetCode 1111 may look intimidating because of its “two strings” requirement, but the underlying logic is a textbook example of *alternating parity* to balance nested structures. By mastering this problem, you’ll gain confidence in:

- Translating problem constraints into simple counters.  
- Recognizing when a stack is overkill.  
- Writing clean, idiomatic code in Java, Python, and C++.

Good luck, and may your parentheses always stay balanced!