---
title: LeetCode 2019. The Score of Students Solving Math Expression - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  The Problem (short recap)

> **Score of Students Solving Math Expression**  
> We are given a simple math expression that contains only single‑digit numbers, ‘+’ and ‘*’.  
> Every student has to evaluate the expression **first all multiplications (left‑to‑right), then all additions (left‑to‑right)**.  
>  
> The array `answers` contains the (unordered) answers submitted by the students.  
>  
> * 5 points – correct answer.  
> * 2 points – answer that could arise if the student applied the operators in the wrong order **but still did the arithmetic correctly**.  
> * 0 points – everything else.  
>  
> Return the total score.

The length of the expression is ≤ 31, the number of operators ≤ 15 and every answer is in `[0, 1000]`.  
The expression is guaranteed to be syntactically correct.

---

## 2.  High‑level idea

| Step | What we do | Why |
|------|------------|-----|
| 1 | **Compute the correct answer** – evaluate `s` with normal precedence. | Gives the 5‑point reference. |
| 2 | **Generate *all* possible results** that can be obtained by any parenthesisation of the expression. | If a student’s answer is in this set but isn’t the correct one, we give 2 points. |
| 3 | **Count points** by iterating over `answers`. | O(n) where *n* ≤ 10⁴. |

The core of the solution is step 2.  
Because the expression contains only single‑digit numbers and at most 15 operators, we can safely enumerate every grouping using recursion + memoisation (dynamic programming on intervals).

---

## 3.  Computing the correct answer

The easiest way is to “do the math as a calculator would”:

```text
total = 0
current_product = first_digit
for every operator o and following digit d
    if o == '+'
         total += current_product
         current_product = d
    else            // o == '*'
         current_product *= d
total += current_product
```

Time complexity : **O(m)**,  *m* = number of operators (≤ 15).

---

## 4.  Generating every possible result

### 4.1  Tokenise

```
nums = [int(ch) for ch in s if ch.isdigit()]
ops  = [ch  for ch in s if ch in '+*']
```

Example: `"7+3*1*2"` → `nums = [7,3,1,2]`, `ops = ['+','*','*']`.

### 4.2  Recursion on intervals

`solve(l, r)`  → *set* of all values that can be obtained from the sub‑expression
`nums[l] op[l] nums[l+1] … op[r] nums[r+1]`.

Base case  
`l > r`  → only one number → `{ nums[l] }`.

Recursive step  

```
result = ∅
for k in [l … r]            // split before op[k]
    left  = solve(l, k-1)
    right = solve(k+1, r)
    for x in left:
        for y in right:
            if ops[k] == '+': result.add(x + y)
            else:            result.add(x * y)
```

Memoisation is essential – we store the result of every `(l, r)` pair in a hash‑map.  
The number of sub‑intervals is O(ops²) ≤ (15)² = 225, so the DP is tiny.

### 4.3  Complexity

* Number of intervals: O(k²) where *k* = number of operators ≤ 15.  
* For each interval we merge two sets; each set contains at most a few hundred integers (the value never exceeds 10⁹ in the statement).  
* Overall time: **O(k³)** in the worst case – well below 10⁵ operations.  
* Memory: **O(k²)** for the memoisation map.

---

## 5.  Full code

Below are clean, ready‑to‑paste solutions in **Java**, **Python** and **C++**.

> **Tip** – all three versions use the *same* algorithm; the only difference is the language‑specific syntax for hash‑maps, recursion and integer sets.

---

### 5.1  Java 17

```java
import java.util.*;
import java.util.stream.*;

class Solution {

    /* ---------- 1. correct answer  ---------- */
    private int correctAnswer(String s) {
        int total = 0;
        int curProd = s.charAt(0) - '0';
        for (int i = 1; i < s.length(); i += 2) {
            char op = s.charAt(i);
            int digit = s.charAt(i + 1) - '0';
            if (op == '+') {
                total += curProd;
                curProd = digit;
            } else {            // '*'
                curProd *= digit;
            }
        }
        return total + curProd;
    }

    /* ---------- 2. all possible results ---------- */
    private List<Integer> nums;        // numbers
    private List<Character> ops;       // operators
    private Map<String, Set<Integer>> memo;

    private Set<Integer> solve(int l, int r) {
        String key = l + "," + r;
        if (memo.containsKey(key)) return memo.get(key);

        Set<Integer> res = new HashSet<>();

        if (l > r) {                         // only one number
            res.add(nums.get(l));
        } else {
            for (int k = l; k <= r; k++) {   // split before op[k]
                Set<Integer> left  = solve(l, k - 1);
                Set<Integer> right = solve(k + 1, r);
                for (int x : left) {
                    for (int y : right) {
                        if (ops.get(k) == '+')
                            res.add(x + y);
                        else
                            res.add(x * y);
                    }
                }
            }
        }
        memo.put(key, res);
        return res;
    }

    /* ---------- public API ---------- */
    public int scoreOfStudents(String s, int[] answers) {
        /* 1. tokenise expression */
        nums = Arrays.stream(s.split("(?=[+*])")).filter(t -> !t.equals("+") && !t.equals("*"))
                     .map(Integer::parseInt).collect(Collectors.toList());
        ops = s.chars()
                .filter(c -> c == '+' || c == '*')
                .mapToObj(c -> (char) c)
                .collect(Collectors.toList());

        memo = new HashMap<>();
        Set<Integer> allVals = solve(0, ops.size() - 1);

        int correct = correctAnswer(s);
        int totalScore = 0;

        for (int ans : answers) {
            if (ans == correct) totalScore += 5;
            else if (allVals.contains(ans)) totalScore += 2;
        }
        return totalScore;
    }
}
```

---

### 5.2  Python 3.10

```python
from functools import lru_cache
from typing import List, Set

class Solution:
    def scoreOfStudents(self, s: str, answers: List[int]) -> int:
        # 1. correct answer
        total, cur_prod = 0, int(s[0])
        for i in range(1, len(s), 2):
            op, d = s[i], int(s[i+1])
            if op == '+':
                total += cur_prod
                cur_prod = d
            else:            # '*'
                cur_prod *= d
        correct = total + cur_prod

        # 2. tokenise
        nums = [int(ch) for ch in s if ch.isdigit()]
        ops  = [ch  for ch in s if ch in '+*']

        # 3. memoised recursion
        @lru_cache(None)
        def solve(l: int, r: int) -> Set[int]:
            if l > r:                      # only one number
                return {nums[l]}
            res = set()
            for k in range(l, r + 1):     # split before ops[k]
                left  = solve(l, k - 1)
                right = solve(k + 1, r)
                if ops[k] == '+':
                    res.update(x + y for x in left for y in right)
                else:
                    res.update(x * y for x in left for y in right)
            return res

        all_vals = solve(0, len(ops) - 1)

        # 4. score students
        score = 0
        for ans in answers:
            if ans == correct:
                score += 5
            elif ans in all_vals:
                score += 2
        return score
```

---

### 5.3  C++ (17)

```cpp
#include <bits/stdc++.h>
using namespace std;

/* ---------- hash for pair<int,int> ---------- */
struct PairHash {
    size_t operator()(const pair<int,int>&p) const noexcept {
        return hash<int>()(p.first) ^ (hash<int>()(p.second) << 1);
    }
};

class Solution {
public:
    int scoreOfStudents(string s, vector<int>& answers) {
        /* 1. correct answer */
        int total = 0, cur = s[0] - '0';
        for (int i = 1; i < (int)s.size(); i += 2) {
            char op = s[i];
            int d = s[i+1] - '0';
            if (op == '+') {
                total += cur;
                cur = d;
            } else { // '*'
                cur *= d;
            }
        }
        int correct = total + cur;

        /* 2. tokenise */
        vector<int> num;
        vector<char> op;
        for (char c : s) {
            if (isdigit(c)) num.push_back(c - '0');
            else            op.push_back(c);
        }

        unordered_map<pair<int,int>, unordered_set<int>, PairHash> memo;

        function<unordered_set<int>(int,int)> dfs = [&](int l, int r) -> unordered_set<int> {
            pair<int,int> key = {l,r};
            auto it = memo.find(key);
            if (it != memo.end()) return it->second;

            unordered_set<int> res;
            if (l > r) {            // only one number
                res.insert(num[l]);
            } else {
                for (int k = l; k <= r; ++k) {     // split before op[k]
                    auto left  = dfs(l, k-1);
                    auto right = dfs(k+1, r);
                    for (int x : left)
                        for (int y : right) {
                            if (op[k] == '+') res.insert(x + y);
                            else             res.insert(x * y);
                        }
                }
            }
            memo[key] = res;
            return res;
        };

        unordered_set<int> all = dfs(0, (int)op.size()-1);

        /* 3. score students */
        int score = 0;
        for (int ans : answers) {
            if (ans == correct)          score += 5;
            else if (all.count(ans))     score += 2;
        }
        return score;
    }
};
```

---

## 6.  What makes the solution *good* ?

1. **Deterministic** – the DP enumerates *every* legal grouping, so no hidden edge‑cases.  
2. **Extremely fast** – even in Python we finish the DP in < 1 ms for the worst‑case expression.  
3. **Modular** – the code cleanly separates the two tasks (normal evaluation + interval DP).  
4. **Interview‑friendly** – a candidate can explain the algorithm in 5‑10 minutes, write the DP skeleton and test it with the example cases.

---

## 7.  Potential pitfalls (the *bad* part)

| Pitfall | What can go wrong | Quick fix |
|---------|------------------|-----------|
| Wrong memo key | Two different intervals are treated as the same → missing results. | Use a unique key – e.g. “l,r” or `pair<int,int>` with a custom hash. |
| Forgetting the *exact* precedence for the 5‑point answer | The 5‑point reference would be wrong and we’ll award 0 points to every student. | Compute the correct answer separately, don’t reuse the DP set. |
| Returning the whole set of *all* parenthesisations *including* the correct value for the 2‑point case | A correct answer would be rewarded twice (5 + 2). | Explicitly skip the correct value when adding 2‑point scores. |
| Not limiting the recursion depth | For very long expressions the stack can overflow in Python. | Use `@lru_cache` or explicit memoisation; in Java the recursion depth is < 16 so it’s safe. |

---

## 8.  The “ugly” part (why the statement feels a bit convoluted)

Leetcode sometimes asks for *all* mathematically possible values that can be obtained by “changing the order of the operators”.  
This is essentially the *Catalan* enumeration of binary trees over the operators – a classic interval DP that most interviewers will recognise as a “well‑known trick” for “different ways to add parentheses” problems.

*The catch*: we must **not** treat “wrong order” as “arbitrary shuffling of the operators” – only those regroupings that keep the relative order of the operands but change the precedence are valid.  
The interval DP covers exactly that, but you need to be careful not to double‑count or miss the correct result.

---

## 9.  Complexity recap

| Sub‑step | Complexity |
|----------|------------|
| Correct answer | **O(k)** |
| DP interval | **O(k³)** (k ≤ 15) |
| Score counting | **O(n)**  (n ≤ 10⁴) |

The whole program runs in well under a millisecond for the maximum test case, making it perfect for both Leetcode and real‑world interviews.

---

## 10.  Final thoughts

> **Good** – a clean, linear‑time solution for the normal evaluation and a tiny DP for the exhaustive parenthesisation.  
> **Bad** – the statement’s wording (“wrong order” vs. “any grouping”) can trip people up; always ask for clarification before coding.  
> **Ugly** – implementing memoised interval DP can look daunting to beginners; a one‑liner `@lru_cache` in Python hides a lot of work, but it’s still an excellent interview technique to show you understand recursion + memoisation.

If you’re preparing for a technical interview, this problem is a perfect showcase of:

1. **Problem decomposition** (separate concerns).  
2. **Classic algorithmic patterns** (Catalan DP).  
3. **Language versatility** – you can deliver the same logic in Java, Python, or C++ with minimal changes.

Good luck, and happy coding!  

---

*For further reading on “different ways to add parentheses” interval DP, check out Leetcode 224 “Basic Calculator II” and Leetcode 241 “Different Ways to Add Parentheses”. These problems share the same underlying DP structure.* 

---  

**Tags**: interval DP, recursion, memoisation, Catalan numbers, Leetcode, interview coding, parenthesisation.