---
title: LeetCode 1996. The Number of Weak Characters in the Game - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap – LeetCode 1996  
**The Number of Weak Characters in the Game**  

> A game contains `n` characters.  
> `properties[i] = [attack_i , defense_i]` is the attack and defense of the *i‑th* character.  
> A character *i* is *weak* if there exists another character *j* such that  
> `attack_j > attack_i` **and** `defense_j > defense_i`.  
> Return the total number of weak characters.

**Constraints**

| `n` | 2 … 10⁵ |
|---|---|
| `attack_i , defense_i` | 1 … 10⁵ |

The problem is a classic 2‑dimensional dominance query and can be solved in **O(n log n)** time with a simple sort + sweep.

---

## 2.  High‑Level Idea – “Sorting + Greedy Sweep”

1. **Sort** all characters by ascending attack.  
   *If two characters have the same attack we must process the one with the **higher** defense first* – otherwise a later character could wrongly be considered weak.  
   Hence the sorting key is  
   ```text
   (attack, -defense)   // ascending attack, descending defense
   ```

2. After sorting, walk the array **backwards** (from highest attack to lowest).  
   Keep a variable `maxDef` that stores the maximum defense seen so far among characters with *higher* attack.  

3. For the current character:  
   * if its defense < `maxDef` → it is weak.  
   * otherwise update `maxDef` with this defense.

4. Count all such weak characters.

The intuition: a character can only be dominated by a later (higher‑attack) character. Because we process in reverse order, `maxDef` already contains the best defense among all higher‑attack characters. A simple comparison tells us if the current character is dominated.

---

## 3.  Code Walk‑through

Below you will find **ready‑to‑copy** implementations in:

| Language | Class/Function | Highlights |
|---|---|---|
| Java | `Solution.numberOfWeakCharacters` | Uses `Arrays.sort` with a custom comparator. |
| Python | `Solution.number_of_weak_characters` | Uses `sort` with key and a reverse loop. |
| C++ | `Solution::numberOfWeakCharacters` | Uses `std::sort` and a single pass. |

Each solution is **O(n log n)** time, **O(1)** extra space (aside from sorting overhead).

---

### 3.1 Java – `Solution.numberOfWeakCharacters`

```java
import java.util.Arrays;

class Solution {
    public int numberOfWeakCharacters(int[][] properties) {
        // Sort by attack ascending; if tie, defense descending
        Arrays.sort(properties, (a, b) -> {
            if (a[0] == b[0]) return Integer.compare(b[1], a[1]); // higher defense first
            return Integer.compare(a[0], b[0]);                  // lower attack first
        });

        int weak = 0;
        int maxDef = 0;           // best defense seen so far among higher attack
        for (int i = properties.length - 1; i >= 0; i--) {
            int def = properties[i][1];
            if (def < maxDef) weak++;
            else maxDef = def;    // new record
        }
        return weak;
    }
}
```

---

### 3.2 Python – `Solution.number_of_weak_characters`

```python
class Solution:
    def numberOfWeakCharacters(self, properties: List[List[int]]) -> int:
        # Sort by attack ascending, defense descending
        properties.sort(key=lambda x: (x[0], -x[1]))
        
        weak = 0
        max_def = 0
        for _, def_val in reversed(properties):
            if def_val < max_def:
                weak += 1
            else:
                max_def = def_val
        return weak
```

---

### 3.3 C++ – `Solution::numberOfWeakCharacters`

```cpp
class Solution {
public:
    int numberOfWeakCharacters(vector<vector<int>>& properties) {
        sort(properties.begin(), properties.end(),
             [](const vector<int>& a, const vector<int>& b) {
                 if (a[0] == b[0]) return a[1] > b[1];   // higher defense first
                 return a[0] < b[0];                    // lower attack first
             });

        int weak = 0;
        int maxDef = 0;
        for (int i = properties.size() - 1; i >= 0; --i) {
            int def = properties[i][1];
            if (def < maxDef) ++weak;
            else maxDef = def;
        }
        return weak;
    }
};
```

---

## 4.  Complexity Analysis

| **Metric** | **Result** |
|---|---|
| Time | **O(n log n)** – dominated by the sorting step. |
| Extra Space | **O(1)** – only a few integer variables. (The sort itself may use O(log n) stack space.) |

---

## 5.  Edge Cases & Pitfalls

| Problem | What Went Wrong | Fix |
|---|---|---|
| Two characters have the same attack | Sorting only by attack can mark the second one weak incorrectly | Sort by defense descending when attack ties |
| Very large `n` (10⁵) | Using an O(n²) nested loop causes TLE | Use the sorted greedy sweep |
| Defensive values at bounds | No special handling needed – algorithm works for all values |
| Empty input | Constraints guarantee `n ≥ 2`, but defensive code can handle `n==0` |

---

## 6.  The “Good, the Bad, and the Ugly”

| Aspect | Good | Bad | Ugly |
|---|---|---|---|
| **Algorithm** | Simple O(n log n) solution – easy to reason about and implement | O(n²) brute force is too slow for 10⁵ | A “magic” O(n) solution that relies on a large fixed array (size 10⁵+2) can waste memory and be confusing |
| **Sorting Key** | `(attack, -defense)` is a well‑known pattern for 2‑D dominance | Forgetting the defense tie‑break → wrong answer | Sorting by attack only then scanning backwards leads to subtle bugs |
| **Space** | Constant auxiliary space | None | Using extra arrays of size 10⁵+2 is unnecessary overhead |
| **Readability** | Clean comparator or key function | Nested loops, too many variables | Overly terse one‑liner code that sacrifices clarity |

---

## 7.  SEO‑Optimized Blog Post Draft

> **Title:**  
> LeetCode 1996 – The Number of Weak Characters: Java, Python & C++ Solutions + Interview Tips  
> **Meta‑Description:**  
> Master LeetCode 1996 in under 15 minutes. Learn the O(n log n) sorting + greedy sweep solution in Java, Python, and C++. Get interview‑ready with pitfalls, edge‑cases, and job‑search SEO tips.

---

### 7.1 Introduction

If you’re prepping for a software‑engineering interview, you’ll almost certainly run across **LeetCode 1996 – “The Number of Weak Characters”**. This medium‑difficulty problem tests your understanding of 2‑D dominance and sorting tricks. Below is a deep dive into the most efficient solution, plus ready‑to‑paste implementations in Java, Python, and C++. I’ll also walk through common pitfalls, why the greedy sweep works, and how this knowledge can boost your resume.

---

### 7.2 Problem Statement (Re‑phrased)

You’re given a 2‑D array `properties`, each element `[attack, defense]`. A character *i* is **weak** if another character *j* has **strictly higher attack AND strictly higher defense**. Count how many characters are weak.

---

### 7.3 Why the O(n log n) Greedy Sweep Works

1. **Dominance in 2‑D** – A character can only be dominated by a character that is to its right when sorted by attack.  
2. **Sorting Key** – When attacks tie, the character with *higher* defense dominates the one with *lower* defense. Sorting by `(attack, -defense)` ensures the “good” one appears first.  
3. **Sweep Backwards** – By walking from the rightmost character to the leftmost, we maintain `maxDef`, the best defense among already‑seen (higher‑attack) characters.  
4. **Single Comparison** – If the current defense < `maxDef`, the current character is weak; otherwise, update `maxDef`.

No additional data structures are required, making the algorithm clean and fast.

---

### 7.4 Code Implementations

> **Tip:** Paste these snippets directly into your LeetCode solution editor.

#### Java

```java
import java.util.Arrays;

class Solution {
    public int numberOfWeakCharacters(int[][] properties) {
        Arrays.sort(properties, (a, b) -> {
            if (a[0] == b[0]) return Integer.compare(b[1], a[1]);
            return Integer.compare(a[0], b[0]);
        });

        int weak = 0;
        int maxDef = 0;
        for (int i = properties.length - 1; i >= 0; i--) {
            int def = properties[i][1];
            if (def < maxDef) weak++;
            else maxDef = def;
        }
        return weak;
    }
}
```

#### Python

```python
class Solution:
    def numberOfWeakCharacters(self, properties: List[List[int]]) -> int:
        properties.sort(key=lambda x: (x[0], -x[1]))
        weak, max_def = 0, 0
        for _, def_val in reversed(properties):
            if def_val < max_def:
                weak += 1
            else:
                max_def = def_val
        return weak
```

#### C++

```cpp
class Solution {
public:
    int numberOfWeakCharacters(vector<vector<int>>& properties) {
        sort(properties.begin(), properties.end(),
             [](const vector<int>& a, const vector<int>& b) {
                 if (a[0] == b[0]) return a[1] > b[1];
                 return a[0] < b[0];
             });

        int weak = 0, maxDef = 0;
        for (int i = properties.size() - 1; i >= 0; --i) {
            int def = properties[i][1];
            if (def < maxDef) ++weak;
            else maxDef = def;
        }
        return weak;
    }
};
```

---

### 7.5 Edge‑Cases You Must Cover

| Case | Why It Matters | Your Code |
|---|---|---|
| `properties = [[2,1],[2,1]]` | Two identical stats – neither is weak | Sorting by defense descending keeps them in the right order |
| Attack ties with decreasing defense | Mis‑order → wrong dominance | Use `-defense` in key/comparator |
| Large `n` | Brute force O(n²) times out | Use the greedy sweep only |

---

### 7.6 Interview‑Level Questions to Ask

During a real interview, the interviewer might probe:

* “What if you need to count *non‑weak* characters instead?”  
* “Can you achieve O(n) time by using a Fenwick tree?”  
* “What if the input size is 10⁶? Memory‑friendly solutions?”

These questions gauge your depth of understanding and adaptability.

---

### 7.7 Adding the Solution to Your Resume

> **Why it matters:** Interviewers love concise, optimal solutions.  
> **Resume wording example:**  
> • Implemented an **O(n log n)** solution for LeetCode problem “The Number of Weak Characters” using sorting + greedy sweep in Java, Python, and C++.  
> • Demonstrated expertise in 2‑D dominance, algorithm design, and time‑space trade‑offs.

Include the GitHub link or code snippet in a portfolio or as a project reference.

---

### 7.8 Closing Thoughts

LeetCode 1996 is a small problem with a big lesson: *ordering matters.* Remember the `(attack, -defense)` trick, sweep from right to left, and you’ll pass this problem in a flash. Apply the same pattern to any “dominance” problem you see in interviews. Good luck—and keep coding!

---

## 8.  Final Words

You now possess:

* A **robust, optimal** solution.  
* **Three language‑specific snippets** that will run fast on LeetCode.  
* **Common pitfalls** to guard against.  
* A **story** you can weave into your interview narrative.

Happy interviewing! 🚀

--- 

*Feel free to adapt the blog post draft to your own voice or platform.*