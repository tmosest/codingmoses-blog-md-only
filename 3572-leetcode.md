---
title: LeetCode 3572. Maximize Y-Sum by Picking a Triplet of Distinct X-Values - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 Leetcode 3572 – *Maximize Y‑Sum by Picking a Triplet of Distinct X‑Values*  
**Languages:** Java | Python | C++  
**Interview Focus:** HashMap, greedy, O(n) time, O(1) space (when possible)  
**SEO Keywords:** Leetcode 3572, Y‑Sum, distinct X values, algorithm interview, job interview coding, hash map, greedy, optimal solution, top 3, Java, Python, C++

---

## 📝 Problem Recap

You are given two integer arrays `x` and `y`, both of length `n`.  
Each pair `(x[i], y[i])` can be seen as a *key‑value* pair.  
Pick three indices `i, j, k` such that the **x‑values are all distinct**  
(`x[i] != x[j] != x[k]`).  
Return the maximum possible sum `y[i] + y[j] + y[k]`.  
If it’s impossible to choose three distinct `x`‑values, return `-1`.

Constraints  
- `3 ≤ n ≤ 10^5`  
- `1 ≤ x[i], y[i] ≤ 10^6`

---

## 🔍 Key Insight

For any given `x`‑value we only care about its **maximum** `y`.  
If we keep the highest `y` for every distinct `x`, the problem becomes:

> *From the set of these maximum `y`‑values, pick the top three and add them.*

So the whole challenge is reduced to:
1. Build a mapping `x → max(y)` – **O(n)**.  
2. Pick the three largest mapped values – **O(k)** where `k` is the number of unique `x` (≤ n).

Below are three idiomatic implementations in Java, Python and C++.

---

## ⚙️ Solution 1 – HashMap + PriorityQueue (Java)

```java
import java.util.*;

class Solution {
    public int maxSumDistinctTriplet(int[] x, int[] y) {
        Map<Integer, Integer> maxY = new HashMap<>();

        // 1. Keep only the maximum y for each distinct x
        for (int i = 0; i < x.length; i++) {
            maxY.put(x[i], Math.max(maxY.getOrDefault(x[i], 0), y[i]));
        }

        // 2. Put all values in a max‑heap and pop the top 3
        PriorityQueue<Integer> pq = new PriorityQueue<>(Collections.reverseOrder());
        pq.addAll(maxY.values());

        if (pq.size() < 3) return -1;

        int sum = 0;
        for (int i = 0; i < 3; i++) sum += pq.poll();
        return sum;
    }
}
```

- **Time**: `O(n + k log k)` (heap operations).  
- **Space**: `O(k)` – the heap plus the hashmap.

---

## 🐍 Solution 2 – HashMap + Sorting (Python)

```python
from typing import List
import heapq

class Solution:
    def maxSumDistinctTriplet(self, x: List[int], y: List[int]) -> int:
        max_y = {}
        for xi, yi in zip(x, y):
            max_y[xi] = max(max_y.get(xi, 0), yi)

        if len(max_y) < 3:
            return -1

        # nlargest from heapq gives the 3 biggest values
        top_three = heapq.nlargest(3, max_y.values())
        return sum(top_three)
```

- **Time**: `O(n + k log k)` – `nlargest` runs in `O(k log 3)` which is effectively `O(k)`.  
- **Space**: `O(k)` – the dictionary.

---

## 🏎️ Solution 3 – Pure Greedy Pass (C++)

```cpp
class Solution {
public:
    int maxSumDistinctTriplet(vector<int>& x, vector<int>& y) {
        int best[3] = {0, 0, 0};
        int key[3]   = {0, 0, 0};

        for (int i = 0; i < x.size(); ++i) {
            if (x[i] == key[0]) best[0] = max(best[0], y[i]);
            else if (x[i] == key[1]) best[1] = max(best[1], y[i]);
            else if (x[i] == key[2]) best[2] = max(best[2], y[i]);
            else {
                // new key – try to insert it in the smallest slot
                int mn = min(best[0], min(best[1], best[2]));
                if (y[i] > mn) {
                    if (mn == best[0]) { best[0] = y[i]; key[0] = x[i]; }
                    else if (mn == best[1]) { best[1] = y[i]; key[1] = x[i]; }
                    else { best[2] = y[i]; key[2] = x[i]; }
                }
            }
        }

        if (best[0] == 0 || best[1] == 0 || best[2] == 0)
            return -1;          // less than 3 unique keys
        return best[0] + best[1] + best[2];
    }
};
```

- **Time**: `O(n)` – one linear scan.  
- **Space**: `O(1)` – only six integers (three values + three keys).  
- **Why it’s the “best” for interviews**:  
  - No extra containers beyond a handful of variables.  
  - Demonstrates a clear greedy strategy that can be explained in a few minutes.

---

## 📌 Why This Greedy Pass is Ideal for Interviews

| Benefit | Explanation |
|---------|-------------|
| **O(n) time** | The interviewer will love seeing you avoid an explicit heap or sorting step. |
| **O(1) auxiliary space** | You show you can keep the algorithm lean. |
| **Easy to explain** | “Keep the best `y` for each `x`; always replace the smallest of the top three if a better one appears.” |

The only trade‑off:  
If you *really* need the worst‑case optimal solution when `k` is huge, you can fall back to the PriorityQueue or sorting approach, but those use extra space.

---

## 🎯 Sample Test Cases

| `x` | `y` | Expected |
|-----|-----|----------|
| `[1,2,1,3,4]` | `[5,7,9,2,10]` | `26` (10+9+7) |
| `[1,1,1,1]` | `[1,2,3,4]` | `-1` (only one distinct `x`) |
| `[3,3,3,4,5]` | `[5,6,7,8,9]` | `24` (9+8+7) |

---

## 🚨 Common Pitfalls

| Pitfall | How to Avoid |
|---------|--------------|
| **Adding all `y`‑values before deduplication** | Always map `x → max(y)` *before* any “take top 3” logic. |
| **Using `int` for sums that can overflow** | With `y[i] ≤ 10^6` and `n ≤ 10^5`, the sum of three values fits in 32‑bit signed int (`≤ 3·10^6`), but be careful in other languages. |
| **Forgetting the “less than three unique keys” check** | Return `-1` early if the mapping size < 3. |

---

## 📈 Complexity Cheat Sheet

| Approach | Time | Extra Space |
|----------|------|-------------|
| HashMap + PQ | `O(n + k log k)` | `O(k)` |
| HashMap + Sort | `O(n + k log k)` | `O(k)` |
| Greedy (O(n) pass) | `O(n)` | `O(1)` |

---

## 🏁 Final Take‑Away

- The *core* of the problem is a simple **max‑value per key** reduction.  
- The greedy approach is the *gold‑standard* for interviewers: linear time, constant auxiliary memory, and no fancy library calls.  
- If you want a quick “production‑ready” solution, the HashMap + PQ or Sort variants work fine too.

---

## 📚 Blog Article – “The Good, the Bad, and the Ugly of Leetcode 3572”

---

### Title  
**Leetcode 3572: The Good, The Bad, and The Ugly – A Deep Dive into Distinct‑Key Triplet Sum**

### Meta Description  
> Learn how to crack Leetcode 3572 in Java, Python, and C++. Discover the optimal greedy O(n) solution, common pitfalls, and interview‑ready code. Boost your coding interview prep and land your next job!

### Introduction (≈ 250 words)

> In the world of coding interviews, **Leetcode 3572 – “Maximize Y‑Sum by Picking a Triplet of Distinct X‑Values”** is a favorite for testing a candidate’s mastery of hash maps and greedy logic. The problem asks you to pair two arrays, keep only the best values per key, and then sum the top three distinct keys.  
> 
> On paper it looks simple, but many candidates stumble over subtle details: forgetting to deduplicate, mismanaging the priority queue, or ignoring the “less than three unique keys” edge case. In this post we’ll break the solution into three sections—**the good**, **the bad**, and **the ugly**—and provide interview‑ready code snippets in Java, Python, and C++ that you can paste into your next Leetcode solution or resume.

### The Good – The Elegant, Interview‑Proof Strategy

*Key takeaways:*
- **O(n) time, O(1) space** (when using the greedy pass).  
- Only one pass through the data.  
- Clear logic: *“Keep the best y per x; replace the smallest of the current top three if you find a better candidate.”*

> In practice, this is the code you’ll see on StackOverflow, discuss in tech blogs, and implement in your personal projects. It’s also the one you should highlight in a job interview because it shows a deep understanding of greedy reasoning.

### The Bad – Over‑engineering with Extra Containers

*Commonly seen approaches:*
- **HashMap + Sorting**: Builds a map of maxima then sorts the values.  
- **HashMap + PriorityQueue**: Inserts all maxima into a heap and polls the top three.

While both work in `O(n + k log k)` time and are easy to write, they add unnecessary memory overhead (`k` extra elements). For interviewers, this may look like a lack of optimization awareness, especially when `n` can be 10⁵.

### The Ugly – Misreading the Constraints

- **Using 64‑bit integers for the sum when 32‑bit is enough**: Wastes type space.  
- **Choosing indices instead of keys**: Leads to off‑by‑one bugs in the distinct‑key check.  
- **Failing to handle duplicate `x`‑values correctly**: The maximum `y` per `x` is the crux; ignoring it yields incorrect results.

These mistakes are typical in quick “copy‑paste” solutions and usually result in a `Wrong Answer` verdict on Leetcode.

---

### Full Code Examples (Java, Python, C++)

> All three snippets implement the **greedy O(n) pass**. You can swap the map/heap versions if you prefer.

#### Java (Greedy O(n))

```java
import java.util.*;

public class Leetcode3572 {
    public int maxSumDistinctTriplet(int[] x, int[] y) {
        // three best values and their corresponding keys
        int a = 0, b = 0, c = 0;
        int key1 = -1, key2 = -1, key3 = -1;

        for (int i = 0; i < x.length; i++) {
            int currX = x[i], currY = y[i];

            if (currX == key1) a = Math.max(a, currY);
            else if (currX == key2) b = Math.max(b, currY);
            else if (currX == key3) c = Math.max(c, currY);
            else {
                // new key – replace the smallest of the three if better
                int mn = Math.min(a, Math.min(b, c));
                if (currY > mn) {
                    if (mn == a) { a = currY; key1 = currX; }
                    else if (mn == b) { b = currY; key2 = currX; }
                    else { c = currY; key3 = currX; }
                }
            }
        }

        return (key1 == -1 || key2 == -1 || key3 == -1) ? -1 : a + b + c;
    }

    public static void main(String[] args) {
        Leetcode3572 solver = new Leetcode3572();
        int[] x = {1, 2, 1, 3, 4};
        int[] y = {5, 7, 9, 2, 10};
        System.out.println(solver.maxSumDistinctTriplet(x, y)); // 26
    }
}
```

#### Python (Greedy O(n))

```python
class Solution:
    def maxSumDistinctTriplet(self, x, y):
        a = b = c = 0
        key1 = key2 = key3 = None

        for xi, yi in zip(x, y):
            if xi == key1:
                a = max(a, yi)
            elif xi == key2:
                b = max(b, yi)
            elif xi == key3:
                c = max(c, yi)
            else:
                mn = min(a, b, c)
                if yi > mn:
                    if mn == a:
                        a, key1 = yi, xi
                    elif mn == b:
                        b, key2 = yi, xi
                    else:
                        c, key3 = yi, xi

        return -1 if not (key1 and key2 and key3) else a + b + c
```

#### C++ (Greedy O(n))

*(See snippet above in the “Pure Greedy Pass” section)*

---

### How to Use These Snippets in Your Resume

1. **Add a “Leetcode 3572 – Distinct‑Key Triplet Sum” section** under “Technical Projects” or “Coding Challenges”.  
2. Briefly describe the greedy logic: *“Linear‑time, constant space algorithm that maintains the top three key‑value pairs.”*  
3. Optionally, link to this blog post or attach a Gist for easy reference.

---

### Closing (≈ 200 words)

> Cracking Leetcode 3572 isn’t just about getting the correct answer; it’s about showing the interviewer that you can think *efficiently*, *cleanly*, and *robustly*.  
> 
> The greedy O(n) pass demonstrates all three: it runs in linear time, uses only primitive variables, and clearly conveys the key idea of “best value per key.”  
> 
> If you’re preparing for a technical interview or polishing your coding résumé, keep this strategy at the top of your mental toolbox. It’s a proven pattern that will impress hiring managers looking for efficient, interview‑ready code.

### Final Call‑to‑Action

> *Ready to tackle Leetcode 3572? Grab the code snippets above, run them against the provided test cases, and add the greedy solution to your GitHub. Then, in your next interview, confidently explain the logic and show you’re not just coding—you’re optimizing.*

### Closing Tags & SEO Keywords

- `leetcode-3572`  
- `coding interview prep`  
- `hash map greedy`  
- `distinct-key triplet sum`  
- `interview algorithm`  

---

### Why You’ll See this in the Job Market

> Recruiters often scan resumes for “Leetcode 3572” solutions that highlight *linear time* and *constant space*. A candidate who can point to the greedy algorithm and explain it within 3–5 minutes demonstrates strong algorithmic maturity—a key differentiator for senior software roles.

---

### Wrap‑up

> By understanding *the good*, *the bad*, and *the ugly*, you can choose the right version of Leetcode 3572 for any situation—whether it’s a quick coding test or a deep technical interview. Paste the provided snippets, practice explaining the logic, and you’ll be ready to tackle the problem with confidence.

### End of Article

--- 

### Call to Action for Readers

- **Practice**: Copy the code into Leetcode and run the sample tests.  
- **Share**: Post your solutions on GitHub or Gist and link them in your résumé.  
- **Interview**: Practice explaining the greedy pass in 30 seconds to a friend or mentor.

---

### Final Note

> *Cracking Leetcode 3572 isn’t just about writing any solution—it's about choosing the most interview‑friendly approach, anticipating edge cases, and articulating your reasoning clearly.*  

---

**Happy coding—and good luck on your next interview!**

--- 

> (Word count ≈ 1,350). This blog post is ready to be shared on Medium, Dev.to, or a personal tech blog to attract recruiters looking for well‑optimized Leetcode solutions.

--- 

Feel free to adapt any part of this article or code to fit your style or the specific job you’re targeting. Good luck!