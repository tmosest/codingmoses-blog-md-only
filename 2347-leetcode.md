---
title: LeetCode 2347. Best Poker Hand - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1️⃣ LeetCode 2347 – Best Poker Hand  
**Problem**:  
Given five cards (`ranks[5]` and `suits[5]`), return the strongest poker hand you can form from the following hierarchy (best → worst):

| Hand | Definition |
|------|------------|
| **Flush** | All five cards share the same suit |
| **Three of a Kind** | At least three cards have the same rank |
| **Pair** | Exactly two cards have the same rank |
| **High Card** | Any other hand |

The cards are guaranteed to be distinct (no duplicate rank+ suit).  
All ranks are `1…13` and suits are one of `'a'…'d'`.

The solution must run in **O(1)** time and use **O(1)** extra space – because the input size is always 5.



--------------------------------------------------------------------

## 2️⃣ Solution Overview (the “Good”)

1. **Count ranks** – an array `cntRank[14]` (index 1‑13).  
2. **Count suits** – an array `cntSuit[4]` (index 0‑3 for `'a'…'d'`).  
3. While iterating through the 5 cards, update both counters.  
4. `maxRank = max(cntRank[i])` – the largest frequency of any rank.  
5. `maxSuit = max(cntSuit[i])` – the largest frequency of any suit.  
6. Decision logic (in order of priority):
   * `maxSuit == 5` → **Flush**
   * `maxRank >= 3` → **Three of a Kind**
   * `maxRank == 2` → **Pair**
   * otherwise → **High Card**

All operations are constant‑time because the input size is fixed.



--------------------------------------------------------------------

## 3️⃣ Code (Java, Python 3, C++)

> **Tip** – Keep the code readable and avoid unnecessary data structures.  
> All three implementations follow the same algorithmic idea.

### 3.1 Java

```java
// 2347. Best Poker Hand – Java
import java.util.*;

public class Solution {
    public String bestHand(int[] ranks, char[] suits) {
        int[] rankCnt = new int[14];          // 1‑13
        int[] suitCnt = new int[4];           // 'a'‑'d'

        for (int i = 0; i < 5; i++) {
            rankCnt[ranks[i]]++;
            suitCnt[suits[i] - 'a']++;
        }

        int maxRank = 0, maxSuit = 0;
        for (int i = 1; i <= 13; i++) maxRank = Math.max(maxRank, rankCnt[i]);
        for (int i = 0; i < 4; i++)     maxSuit = Math.max(maxSuit, suitCnt[i]);

        if (maxSuit == 5)          return "Flush";
        if (maxRank >= 3)          return "Three of a Kind";
        if (maxRank == 2)          return "Pair";
        return "High Card";
    }

    public static void main(String[] args) {
        Solution sol = new Solution();
        System.out.println(sol.bestHand(new int[]{13,2,3,1,9},
                                       new char[]{'a','a','a','a','a'})); // Flush
    }
}
```

### 3.2 Python 3

```python
# 2347. Best Poker Hand – Python 3
from collections import Counter
from typing import List

class Solution:
    def bestHand(self, ranks: List[int], suits: List[str]) -> str:
        # Count ranks & suits
        rank_cnt = Counter(ranks)
        suit_cnt = Counter(suits)

        if len(suit_cnt) == 1:                 # all suits identical
            return "Flush"

        max_rank = max(rank_cnt.values())
        if max_rank >= 3:
            return "Three of a Kind"
        if max_rank == 2:
            return "Pair"
        return "High Card"

# Example
if __name__ == "__main__":
    sol = Solution()
    print(sol.bestHand([4,4,2,4,4], ['d','a','a','b','c']))  # Three of a Kind
```

### 3.3 C++

```cpp
// 2347. Best Poker Hand – C++
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string bestHand(vector<int>& ranks, vector<char>& suits) {
        int rankCnt[14] = {0};          // 1‑13
        int suitCnt[4]  = {0};          // 'a'‑'d'

        for (int i = 0; i < 5; ++i) {
            rankCnt[ranks[i]]++;
            suitCnt[suits[i] - 'a']++;
        }

        int maxRank = 0, maxSuit = 0;
        for (int i = 1; i <= 13; ++i) maxRank = max(maxRank, rankCnt[i]);
        for (int i = 0; i < 4; ++i)    maxSuit = max(maxSuit, suitCnt[i]);

        if (maxSuit == 5) return "Flush";
        if (maxRank >= 3) return "Three of a Kind";
        if (maxRank == 2) return "Pair";
        return "High Card";
    }
};

int main() {
    Solution s;
    vector<int> ranks   = {13,2,3,1,9};
    vector<char> suits = {'a','a','a','a','a'};
    cout << s.bestHand(ranks, suits) << endl;   // Flush
}
```

All three solutions satisfy the required **O(1) time / O(1) space** guarantees and are perfect for a coding‑interview.



--------------------------------------------------------------------

## 4️⃣ The “Bad” & “Ugly” – What NOT to do

| Problem | Inefficient or Over‑Complex Approach | Why it’s bad |
|---------|-------------------------------------|--------------|
| **Using a map for suits** | `unordered_map<char, int> suitCnt;` | Adds O(1) overhead but still fine – still O(1) in practice, but extra code & memory. |
| **Sorting the whole array** | `sort(ranks.begin(), ranks.end());` | O(n log n) when *n* is 5 – unnecessary because we already know the hierarchy. |
| **Recursive back‑tracking** | Building all subsets of 5 cards | O(2⁵) ≈ 32 operations – far more complex than a single pass. |
| **O(n²) nested loops** | Counting duplicates by double loop | Still works but harder to read and can be error‑prone. |

**Takeaway** – In interviews, *simplicity wins*.  A single linear pass that updates two counters is the cleanest, safest, and most interview‑friendly solution.



--------------------------------------------------------------------

## 5️⃣ Complexity Analysis

| Language | Time | Extra Space |
|----------|------|-------------|
| Java | **O(1)** (5 iterations + 17 fixed‑size loops) | **O(1)** |
| Python | **O(1)** (5‑element `Counter`) | **O(1)** |
| C++ | **O(1)** | **O(1)** |

> **Why O(1) matters** – In a coding‑interview, you’re often asked to think about the *big‑O* of your solution.  
> Because the input size is fixed, the algorithm is constant‑time, but we still must *justify* that we didn’t use an unbounded data structure.



--------------------------------------------------------------------

## 6️⃣ Testing the Code

```python
# All languages share the same test cases

tests = [
    # ranks, suits, expected
    ([13, 2, 3, 1, 9],  ['a','a','a','a','a'], "Flush"),
    ([4, 4, 2, 4, 4],   ['d','a','a','b','c'], "Three of a Kind"),
    ([4, 4, 2, 5, 6],   ['d','a','a','b','c'], "Pair"),
    ([2, 5, 7, 9, 11],  ['a','b','c','d','a'], "High Card"),
]
```

Run each implementation against these cases; all should print the expected hand.



--------------------------------------------------------------------

## 7️⃣ Blog Article – “The Good, The Bad, & The Ugly of Best Poker Hand”  

> **SEO Focus** – *Best Poker Hand LeetCode 2347, Java solution, Python solution, C++ solution, interview algorithm, O(1) time, job interview success, software engineer resume, data structures*.

---

### 📌 Title  
**Best Poker Hand (LeetCode 2347) – How a 5‑Card Evaluation Became a Job‑Interview Goldmine**

### 📚 Introduction  

During a recent interview, I tackled **LeetCode 2347 – Best Poker Hand**. Though the input is tiny (exactly 5 cards), the problem is a great teaching moment for array counting, priority decision logic, and constant‑time algorithms. In this article I’ll walk you through the **good**, **bad**, and **ugly** ways to solve it, give you clean code in **Java**, **Python**, and **C++**, and explain why this problem is a perfect showcase on your résumé.

---

### 🏆 Problem Recap  

| Hand | Rank |
|------|------|
| **Flush** | 5 cards same suit |
| **Three of a Kind** | ≥ 3 cards same rank |
| **Pair** | Exactly 2 cards same rank |
| **High Card** | Anything else |

All ranks: 1‑13; all suits: 'a'–'d'.  
You return the strongest hand according to the hierarchy above.

> *Why this matters:*  This problem tests **array/hash counting**, **priority decision making**, and **time‑space analysis** – all classic interview skills.



---

### 🔎 Naïve Approaches (The “Bad”)

| Approach | Why it’s not ideal |
|----------|--------------------|
| **Sorting the ranks** | Adds O(n log n) overhead, though *n = 5* |
| **Using `Map<char, int>` for suits** | Still O(1) but more code and slightly larger constant factors |
| **Recursion / back‑tracking** | O(2⁵) ≈ 32 states – unnecessary complexity |

These solutions compile, run, and even pass all tests, but they’re over‑engineering a trivial constant‑size problem. Interviewers prefer concise, predictable solutions that illustrate clear thinking.



---

### 🔥 Optimal O(1) Counter‑Based Solution (The “Good”)

1. **Two fixed‑size arrays** – `rankCnt[14]` and `suitCnt[4]`.  
2. One linear pass updates both counters.  
3. `maxRank` and `maxSuit` give the hand strength instantly.  
4. Decision tree respects the hand hierarchy.

*Benefits*  
* **Constant time** – 5 iterations + 17 fixed‑size loops = ~22 operations.  
* **Constant space** – 18 integers + a few temporaries.  
* **No hash overhead** – perfect for interviews.  

**Java, Python, and C++** code all use this idea – see section 3.



---

### ⚠️ Common Pitfalls (The “Ugly”)

| Mistake | Symptom | Fix |
|---------|---------|-----|
| **Using `unordered_map` for suits** | Still correct but slower & harder to read | Replace with a 4‑size array |
| **Checking for a pair after a three‑of‑a‑kind** | Might mistakenly report “Pair” first | Always check `Flush` → `Three of a Kind` → `Pair` |
| **Off‑by‑one errors in array indices** | Index 0 used for rank 0 → array out‑of‑bounds | Use ranks 1‑13 or shift by‑1 consistently |
| **Not handling the “Flush” priority** | Might return “Three of a Kind” when a Flush exists | Compare `maxSuit` first |

Avoiding these mistakes ensures you stay on the right side of the hand hierarchy.



---

### 📊 Complexity Summary

| Language | Time | Space | Why it’s interview‑friendly |
|----------|------|-------|-----------------------------|
| **Java** | **O(1)** | **O(1)** | Uses primitive arrays → fast & clear |
| **Python** | **O(1)** | **O(1)** | `Counter` + simple `if` chain – Pythonic yet optimal |
| **C++** | **O(1)** | **O(1)** | Static arrays + `max` loops – no dynamic allocation |

The *O(1)* guarantees demonstrate that you understand the constraints and can design an algorithm that runs in constant time, a critical interview skill.



---

### 💡 Interview Take‑aways

1. **Read the hierarchy carefully** – priority matters!  
2. **Prefer array counting** over generic maps when indices are known.  
3. **Explain your decision tree** – interviewers love to hear the “why” before the code.  
4. **Keep edge cases in mind** – here, the only real edge is the all‑same‑suit case.  
5. **Showcase your code** – provide a concise, clean solution in the language of choice (Java, Python, C++).  

When you list this problem on your résumé, you can phrase it as:  

> *“Implemented an O(1) solution for LeetCode 2347 “Best Poker Hand”, using fixed‑size arrays to achieve constant‑time rank and suit counting.”*



---

### 📚 Full Code Snippets

> **Java** – Fixed‑size counter implementation

```java
public String bestHand(int[] ranks, char[] suits) {
    int[] rankCnt = new int[14];
    int[] suitCnt = new int[4];

    for (int i = 0; i < 5; i++) {
        rankCnt[ranks[i]]++;
        suitCnt[suits[i] - 'a']++;
    }

    int maxRank = 0, maxSuit = 0;
    for (int i = 1; i <= 13; i++) maxRank = Math.max(maxRank, rankCnt[i]);
    for (int i = 0; i < 4; i++)     maxSuit = Math.max(maxSuit, suitCnt[i]);

    if (maxSuit == 5)          return "Flush";
    if (maxRank >= 3)          return "Three of a Kind";
    if (maxRank >= 2)          return "Pair";
    return "High Card";
}
```

> **Python** – `Counter` + priority chain

```python
def bestHand(ranks, suits):
    rankCnt = Counter(ranks)
    suitCnt = Counter(suits)

    if max(suitCnt.values()) == 5:
        return "Flush"
    if 3 in rankCnt.values():
        return "Three of a Kind"
    if 2 in rankCnt.values():
        return "Pair"
    return "High Card"
```

> **C++** – Static array counters

```cpp
string bestHand(vector<int> ranks, vector<char> suits) {
    int rankCnt[14] = {0};
    int suitCnt[4]  = {0};

    for (int i = 0; i < 5; ++i) {
        rankCnt[ranks[i]]++;
        suitCnt[suits[i] - 'a']++;
    }

    int maxRank = 0, maxSuit = 0;
    for (int i = 1; i <= 13; ++i) maxRank = max(maxRank, rankCnt[i]);
    for (int i = 0; i < 4; ++i)     maxSuit = max(maxSuit, suitCnt[i]);

    if (maxSuit == 5)          return "Flush";
    if (maxRank >= 3)          return "Three of a Kind";
    if (maxRank >= 2)          return "Pair";
    return "High Card";
}
```

---

### 🚀 Wrap‑up  

*Best Poker Hand* may seem like a toy problem, but its solution spotlights array counting, priority checks, and constant‑time complexity—all of which are golden skills for a **software engineer** or **full‑stack developer**.  

Whether you code in **Java**, **Python**, or **C++**, this problem is a concise way to show interviewers you can take a simple data‑set, apply efficient counters, and respect a strict hierarchy.  

If you’re prepping for your next coding interview, try this solution on LeetCode or HackerRank and add the succinct *O(1)* story to your résumé – it’ll get noticed.

---



---

### 🏁 Conclusion  

LeetCode 2347 demonstrates that *small inputs can still teach big lessons.*  
By choosing the optimal counter‑based solution, avoiding common pitfalls, and articulating the logic clearly, you can turn a 5‑card evaluation into a standout interview achievement.  
Drop the code into your portfolio, share it on GitHub, and watch your interview scores—and your résumé—climb.



---



> **Author’s Note** – If you’d like a live coding session or deeper dive into similar hand‑evaluation problems, feel free to reach out.  I’m happy to review or mentor!