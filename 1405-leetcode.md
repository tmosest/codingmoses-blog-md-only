---
title: LeetCode 1405. Longest Happy String - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 1405. Longest Happy String – 3‑Language Solution + SEO‑Optimized Blog

Below you’ll find a complete, production‑ready implementation of the **Longest Happy String** problem in **Java, Python, and C++**.  
After the code, a deep‑dive blog article explains the algorithm, pros/cons, pitfalls, and why mastering this problem will make your LeetCode profile shine to recruiters.

---

## 🔧 Code

> **Signature**  
> `public String longestDiverseString(int a, int b, int c)`

### 1️⃣ Java (JDK 17+)

```java
import java.util.*;

public class Solution {
    public String longestDiverseString(int a, int b, int c) {
        // Max‑heap (priority queue) ordered by remaining count
        PriorityQueue<int[]> pq = new PriorityQueue<>(
            (x, y) -> Integer.compare(y[0], x[0]));   // descending

        if (a > 0) pq.offer(new int[]{a, 'a'});
        if (b > 0) pq.offer(new int[]{b, 'b'});
        if (c > 0) pq.offer(new int[]{c, 'c'});

        StringBuilder sb = new StringBuilder();

        while (!pq.isEmpty()) {
            int[] first = pq.poll();                     // most frequent
            int count1 = first[0];
            char ch1 = (char) first[1];

            // If we already have two consecutive ch1, use the second most frequent
            if (sb.length() >= 2 &&
                sb.charAt(sb.length() - 1) == ch1 &&
                sb.charAt(sb.length() - 2) == ch1) {

                if (pq.isEmpty()) break;                // nothing else to use

                int[] second = pq.poll();
                char ch2 = (char) second[1];

                sb.append(ch2);
                if (--second[0] > 0) pq.offer(second);  // push back if still available

                pq.offer(first);                        // put first back
            } else {
                sb.append(ch1);
                if (--count1 > 0) pq.offer(new int[]{count1, ch1});
            }
        }
        return sb.toString();
    }

    // For quick testing
    public static void main(String[] args) {
        System.out.println(new Solution().longestDiverseString(1, 1, 7));
        System.out.println(new Solution().longestDiverseString(7, 1, 0));
    }
}
```

### 2️⃣ Python 3

```python
import heapq
from typing import List

class Solution:
    def longestDiverseString(self, a: int, b: int, c: int) -> str:
        # Python's heapq is a min‑heap; store negative counts for max‑heap
        pq: List[tuple[int, str]] = []
        for count, char in ((a, 'a'), (b, 'b'), (c, 'c')):
            if count > 0:
                heapq.heappush(pq, (-count, char))

        res = []

        while pq:
            count1, ch1 = heapq.heappop(pq)
            count1 = -count1  # back to positive

            # If last two chars are ch1, use the next most frequent
            if len(res) >= 2 and res[-1] == ch1 == res[-2]:
                if not pq:
                    break  # nothing else to use

                count2, ch2 = heapq.heappop(pq)
                count2 = -count2

                res.append(ch2)
                if count2 - 1:
                    heapq.heappush(pq, (-(count2 - 1), ch2))

                # put first back
                heapq.heappush(pq, (-count1, ch1))
            else:
                res.append(ch1)
                if count1 - 1:
                    heapq.heappush(pq, (-(count1 - 1), ch1))

        return ''.join(res)

# quick demo
if __name__ == "__main__":
    s = Solution()
    print(s.longestDiverseString(1, 1, 7))
    print(s.longestDiverseString(7, 1, 0))
```

### 3️⃣ C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string longestDiverseString(int a, int b, int c) {
        // Max‑heap of pairs {count, char}
        priority_queue<pair<int, char>> pq;
        if (a) pq.push({a, 'a'});
        if (b) pq.push({b, 'b'});
        if (c) pq.push({c, 'c'});

        string res;

        while (!pq.empty()) {
            auto [cnt1, ch1] = pq.top(); pq.pop();

            // Check for three consecutive same characters
            if (res.size() >= 2 && res[res.size()-1] == ch1 && res[res.size()-2] == ch1) {
                if (pq.empty()) break;           // nothing else to use

                auto [cnt2, ch2] = pq.top(); pq.pop();
                res += ch2;
                if (--cnt2) pq.push({cnt2, ch2});

                pq.push({cnt1, ch1});            // put first back
            } else {
                res += ch1;
                if (--cnt1) pq.push({cnt1, ch1});
            }
        }
        return res;
    }
};

int main() {
    Solution sol;
    cout << sol.longestDiverseString(1, 1, 7) << endl;
    cout << sol.longestDiverseString(7, 1, 0) << endl;
    return 0;
}
```

> All three implementations run in **O(a + b + c)** time and **O(1)** extra space (besides the heap of at most three elements).

---

## 📚 Blog Article – “The Good, The Bad, & The Ugly of LeetCode’s Longest Happy String”

> **Keyword Focus**: *Longest Happy String*, *LeetCode*, *Greedy Algorithm*, *Priority Queue*, *Interview Prep*, *Job Hunting*.

---

### 1️⃣ Introduction

LeetCode’s **1405 – Longest Happy String** is a classic greedy problem that tests your ability to balance *frequency* and *constraints*. While it may appear simple at first glance, mastering this problem reveals deep insights into:

- **Greedy Decision Making**  
- **Data Structures** (Max‑Heap / Priority Queue)  
- **Edge‑Case Handling** (when two or three same letters are about to appear)

Getting this problem right can make a recruiter’s eyes pop in a pile of resumes, especially when you discuss it in a technical interview.

---

### 2️⃣ Problem Restatement

> **Goal**: Build the longest possible string composed of ‘a’, ‘b’, and ‘c’ such that:
> 1. No “aaa”, “bbb”, or “ccc” appears as a substring.  
> 2. The string uses at most `a` ‘a’s, `b` ‘b’s, and `c` ‘c’s.  
> 3. Return *any* longest string (empty if impossible).

**Constraints**  
`0 <= a, b, c <= 100`, `a + b + c > 0`.

---

### 3️⃣ The “Good” – Why a Greedy + Heap Works

| Reason | Explanation |
|--------|-------------|
| **Optimal Substructure** | At each step, picking the most frequent character is always safe – any other choice would only reduce the remaining length. |
| **Simple State** | Only the current counts and the last two characters matter. |
| **Linear Complexity** | Each character insertion is O(log 3) ≈ O(1); total time O(n). |
| **Space Efficiency** | The heap holds at most three elements; the output string is the only large structure. |
| **Clear Implementation** | The logic maps naturally to “take, check, put back” with a priority queue. |

> **Takeaway**: Greedy with a max‑heap gives an **O(n)** solution that is trivial to reason about and fast in practice.

---

### 4️⃣ The “Bad” – Common Pitfalls

| Pitfall | What Goes Wrong | Fix |
|---------|-----------------|-----|
| **Not checking two consecutive same chars** | You may produce “aaa” after adding a third ‘a’. | After each append, verify the last two characters; if they equal the current candidate, switch to the second most frequent. |
| **Ignoring remaining counts** | Popping from the heap then forgetting to push back when count > 0. | After decrementing, re‑insert into the heap only if the new count is still positive. |
| **Edge cases with one or two letters** | For `(a=0, b=1, c=2)`, you must handle the “only one letter left” scenario gracefully. | The algorithm naturally exits when the heap is empty; still ensure the loop condition checks heap emptiness after a forced swap. |
| **Using a min‑heap without negation** | Python’s `heapq` is a min‑heap; forgetting to store negatives leads to wrong ordering. | Store negative counts or use `heapq.nlargest`. |
| **Time‑limit mis‑interpretation** | Assuming O(n²) due to repeated scans for the most frequent letter. | Avoid linear scans; always use the heap’s `poll()`/`offer()` operations. |

---

### 5️⃣ The “Ugly” – Advanced Variations & Extensions

1. **Multiple Test Cases**  
   - LeetCode’s “solve all test cases” wrapper may push the solution into a `for` loop; ensure you reset the heap for each test.  

2. **Large Counts (1 000 000)**  
   - Even though the constraints say ≤ 100, a real interview may present larger numbers.  
   - The algorithm still works because the heap operations are independent of the count magnitude; only the final string length changes.

3. **Language‑Specific Nuances**  
   - **Java**: Remember that `char` is 16‑bit; store it as `int` in the heap to maintain ordering.  
   - **Python**: Use `heapq.heappush`/`heappop` with negative counts; avoid `int`‑to‑`char` conversions.  
   - **C++**: Use `std::priority_queue` of `pair<int, char>`.  

---

### 5️⃣ Interview‑Ready Discussion

During a technical interview, you can walk through this problem in the following style:

1. **Clarify Constraints**  
   - “Are we allowed to use all three letters? What about when one count is 0?”

2. **Explain the Greedy Choice**  
   - “I’ll always pick the character with the highest remaining frequency.”

3. **Show the Heap Logic**  
   - “I’ll maintain a max‑heap of `{count, letter}` and re‑insert after each decrement.”

4. **Highlight the Two‑Character Check**  
   - “If the last two characters are the same as my candidate, I’ll temporarily pop the second most frequent and use it.”

5. **Provide Edge‑Case Examples**  
   - “For input (7, 1, 0) the algorithm will produce ‘aabaaa’ as the longest valid string.”

6. **Time & Space Analysis**  
   - “O(a + b + c) time, O(1) additional space.”

Recruiters love when candidates can discuss *why* the solution is optimal, not just *how* it works.

---

### 6️⃣ Why This Problem Helps You Land a Job

| Skill | How the Problem Trains It |
|-------|--------------------------|
| **Algorithmic Thinking** | Demonstrates you can formalize a problem into an optimal greedy strategy. |
| **Data Structure Proficiency** | Shows comfort with priority queues, which are common in system‑design and real‑world coding. |
| **Code Quality** | Your solution is clean, maintainable, and well‑documented – traits recruiters value in production code. |
| **Time Management** | Solving a 100‑point problem in under a minute signals strong problem‑solving speed. |

> *When you nail 1405, you’re showing you can handle *constraints* and *maximization* in a single pass – a rare combination that stands out on a tech resume.*

---

### 7️⃣ Bonus: Extending to 5+ Characters

If you’re curious, the same greedy logic scales to **n‑letter happy strings** (`n <= 26`). The only difference is:

- The heap will grow to size `n`.  
- The “two‑consecutive‑same” rule remains identical.  
- Complexity stays **O(total length)**.

---

### 8️⃣ Final Verdict

- **The Good** – Fast, optimal, and conceptually elegant.  
- **The Bad** – Requires careful handling of the “two consecutive same” rule and heap re‑insertion.  
- **The Ugly** – None! (When you get the logic right, the implementation is straightforward.)

**Master this problem, and you’ll not only ace LeetCode’s difficulty ladder but also give interviewers a *ready‑made example* of **greedy reasoning + priority queue**—a recipe recruiters love.

---

## 🚀 Wrap‑up

| ✅   | ✅   | ✅   |
|------|------|------|
| Java, Python, & C++ solutions with  **O(n)** time | Detailed algorithmic analysis | Job‑hunting insight and interview strategy |

Feel free to copy, run, and tweak the code snippets.  
Happy coding, and may your next interview be “happy” for all the right reasons!