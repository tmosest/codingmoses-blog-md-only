---
title: LeetCode 3592. Inverse Coin Change - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🔎 Problem Recap – LeetCode 3592 “Inverse Coin Change”

> **Goal** – Given a 1‑indexed array `numWays` (length *N*), where `numWays[i]` is the number of ways to reach amount `i+1` using **unknown** coin denominations, recover *one* set of denominations that could produce exactly that array.  
> If no such set exists, return an empty array.

| Term | Meaning |
|------|---------|
| `numWays[i]` | Ways to make amount `i+1` (the array is 0‑indexed in code) |
| Denomination | Positive integer ≤ *N* |
| Coin supply | Infinite for each denomination |

The classic **forward** DP for “Coin Change II” (LeetCode 518) is:

```text
dp[0] = 1
for coin in coins:
    for j = coin … N:
        dp[j] += dp[j-coin]
```

Our job is to *reverse* that process.

---

## 🧠 Intuition – Why the reverse DP works

1. **Adding a new coin `i`**  
   The first time we introduce a coin with value `i`, it contributes exactly **one** new way to make amount `i` (using a single coin).  
   Mathematically: `dp[i]` increases by `dp[0] = 1`.

2. **Effect on other amounts**  
   After the coin is added, every amount `j ≥ i` gets updated:  
   `dp[j] += dp[j-i]`.  
   This is the same update that the forward DP would perform.

3. **Key Observation**  
   While iterating amounts from 1 to *N*, if we **haven’t** added coin `i` yet, the current `dp[i]` will be **exactly** one less than the required `numWays[i-1]` *iff* a coin `i` must exist.  
   In that case we add coin `i`, perform the DP update, and then verify that `dp[i]` now matches `numWays[i-1]`.

4. **Impossible Scenarios**  
   If at any point `dp[i]` is *not* `numWays[i-1]` after a possible coin addition, the array is inconsistent → return `[]`.

Because we always examine amounts in ascending order, the list we build is already sorted.

---

## ✨ “Good” solution – The O(*N*²) reverse DP

The algorithm runs in **O(N²)** time (every coin insertion triggers an update over the remaining amounts) and **O(N)** space.

> **Why this is “good”**  
> *Clear logic* – one pass over amounts, a single DP array, easy to reason about.  
> *Deterministic output* – always returns the smallest set of denominations that satisfies the data.  
> *Safe for LeetCode* – satisfies the constraints (`N ≤ 100`).

### Java

```java
import java.util.*;

public class Solution {
    public static List<Integer> findCoins(int[] numWays) {
        int n = numWays.length;                     // amounts 1 … n
        int[] dp = new int[n + 1];                  // dp[0] … dp[n]
        List<Integer> coins = new ArrayList<>();

        dp[0] = 1;                                  // base case

        for (int amt = 1; amt <= n; amt++) {
            int ways = numWays[amt - 1];            // required ways for amount = amt

            // If a coin of value 'amt' must exist
            if (ways > 0 && dp[amt] == ways - 1) {
                coins.add(amt);
                for (int j = amt; j <= n; j++) {
                    dp[j] += dp[j - amt];
                }
            }

            // After (maybe) adding the coin, the count must match
            if (dp[amt] != ways) {
                return new ArrayList<>();           // impossible
            }
        }
        return coins;
    }
}
```

### Python

```python
def findCoins(numWays):
    """
    :param numWays: List[int] – 0‑indexed array, amount i+1 corresponds to numWays[i]
    :return: List[int] – list of denominations (ascending order) or empty list
    """
    n = len(numWays)
    dp = [0] * (n + 1)        # dp[0] … dp[n]
    dp[0] = 1
    coins = []

    for amt in range(1, n + 1):          # amt == 1 … n
        ways = numWays[amt - 1]

        if ways > 0 and dp[amt] == ways - 1:
            coins.append(amt)
            for j in range(amt, n + 1):
                dp[j] += dp[j - amt]

        if dp[amt] != ways:              # consistency check
            return []

    return coins
```

### C++

```cpp
#include <vector>
using namespace std;

class Solution {
public:
    vector<int> findCoins(vector<int>& numWays) {
        int n = numWays.size();               // amounts 1 … n
        vector<int> dp(n + 1, 0);
        vector<int> coins;
        dp[0] = 1;

        for (int amt = 1; amt <= n; ++amt) {
            int ways = numWays[amt - 1];

            if (ways > 0 && dp[amt] == ways - 1) {
                coins.push_back(amt);
                for (int j = amt; j <= n; ++j)
                    dp[j] += dp[j - amt];
            }

            if (dp[amt] != ways)
                return {};   // impossible
        }
        return coins;
    }
};
```

> **Tip** – All three implementations share the *exact same* logic, only language syntax differs.

---

## 💥 “Bad” & “Ugly” – What *not* to do

| Approach | Complexity | Why it’s bad/ugly |
|----------|------------|-------------------|
| **Brute‑Force Enumeration** – try every subset of `{1,…,N}` and run the forward DP for each candidate set. | `O(2^N * N)` | Exponential time – impossible for *N* = 100. |
| **Recursive Backtracking with Memoisation** – DFS over amounts, choose “add coin” or “skip coin” decisions, cache states. | Roughly `O(2^N)` (state explosion) | Still exponential; hard to understand under time pressure. |
| **Greedy “largest‑first”** – always try to add the largest missing denomination. | `O(N²)` but may produce wrong results | Fails on many inputs; not deterministic. |

**Key lesson** – Interviewers will *appreciate* a concise algorithmic idea over brute‑force hacks. The reverse DP is the “good” solution that is both *fast* and *intuitive*.

---

## 📑 Implementation Checklist

| ✅ Item | Details |
|--------|---------|
| **Input handling** | The array `numWays` is 0‑indexed; amount `i+1` maps to `numWays[i]`. |
| **Base case** | `dp[0] = 1`. |
| **Adding a coin** | Only when `dp[amt] == ways-1` *and* `ways > 0`. |
| **Consistency check** | After possible coin insertion, `dp[amt]` must equal `ways`. |
| **Return value** | List/vector of denominations in ascending order (automatic by construction). |
| **Edge cases** | *All zeroes*, *increasing*, *decreasing*, *duplicate ways*, *impossible arrays* – all handled by the consistency check. |

---

## ⏱ Complexity

- **Time** – `O(N²)` (worst case: every coin triggers a full DP update of the remaining amounts).  
  With *N* ≤ 100 this is trivial for any judge.  
- **Space** – `O(N)` for the `dp` array plus `O(N)` for the output list.

---

## 🧪 Sample Tests

| `numWays` | Expected output | Explanation |
|-----------|-----------------|-------------|
| `[0, 1, 2, 3, 4]` | `[2]` | Only coin 2 is needed. |
| `[1, 2, 2, 3, 4]` | `[1, 2, 5]` | Classic example from the problem statement. |
| `[0, 0, 0, 0]` | `[]` | No coin can produce any way – consistent array. |
| `[1, 1, 1, 1]` | `[]` | Impossible: if coin 1 existed, amount 1 would have 1 way, but adding it would also create ways for 2,3,4. |

Run the provided code snippets in your favorite IDE or online compiler to verify.

---

## 🎯 Why this matters for a **Job Interview**

1. **Shows DP mastery** – You demonstrate understanding of how classic algorithms can be *inverted* or *adapted*.
2. **Problem‑solving mindset** – You’re not just coding; you’re formulating a clean logic that handles corner cases.
3. **Efficiency** – An O(*N*²) solution with *N* = 100 is easily within limits – interviewers love solutions that scale, even for small constraints.
4. **Communication** – Explaining the intuition step‑by‑step (as we did above) is a key interview skill.  
   *Practice*: “Start by describing the forward DP, then show how adding a coin changes only one value by +1, etc.”  
5. **Edge‑case handling** – Highlight that the consistency check catches impossible arrays, which shows defensive coding.

> **Pro tip:** When asked to solve a new DP problem, always think:  
> *What is the invariant that changes when you add a new element?*  
> *Can you “detect” that change by looking at the next state?*  
> This pattern works in many reverse‑DP interview problems (e.g., “Minimum Add to Make Parentheses Valid” in a flipped form).

---

## 📝 SEO‑Optimized Blog Outline

> **Title**: *Reverse‑Engineering Coin Change – LeetCode 3592 “Inverse Coin Change” – A Complete Guide for Coding Interviews*  
> **Meta‑description**: Learn the reverse‑DP trick to solve LeetCode 3592, understand the underlying theory, and see ready‑to‑copy Java, Python, and C++ implementations. Perfect for software‑engineering interview prep.  

### 1. Introduction (≈300 words)

- Hook: “Ever seen a puzzle where you’re given the result but must find the hidden process?”  
- Mention LeetCode 3592 & its popularity in coding‑interviews.  
- State the problem in plain English.  

### 2. Why “Inverse Coin Change” Is a Great Interview Question

- Relates to classic dynamic programming (Coin Change II).  
- Tests ability to *think backwards*, a rare skill.  
- Gives you a chance to talk about infinite supply, integer constraints, and array indexing.  

### 3. “Good” – The Reverse DP Solution (≈400 words)

- Present the intuition (see above).  
- Walk through the algorithm step‑by‑step on a concrete example.  
- Emphasise that the list is built in ascending order and the DP array never needs to be reset.  

### 4. Code Snippets (≈200 words per language)

- Show Java, Python, C++ side‑by‑side.  
- Add `#` comment lines explaining each block.  
- Encourage the reader to copy‑paste and test.  

### 5. “Bad” & “Ugly” – What *not* to do (≈250 words)

- Briefly describe brute‑force and backtracking.  
- Explain why they’re ineffective.  
- Encourage focusing on the O(N²) reverse‑DP instead.  

### 6. Time & Space Complexity (≈200 words)

- Provide the math.  
- Discuss why it’s acceptable for *N* ≤ 100.  

### 7. Edge Cases & Defensive Coding (≈200 words)

- List all scenarios the consistency check covers.  
- Mention how to avoid off‑by‑one bugs with array indexing.  

### 8. Preparing for the Interview

- Practice explaining the logic aloud.  
- Write a small test harness to verify consistency.  
- Highlight how to adapt the solution to other reverse‑DP problems.  

### 9. Conclusion (≈200 words)

- Summarise the trick, highlight key take‑aways.  
- Invite readers to try the problem on LeetCode.  
- Provide a final call‑to‑action: “Share your solution in the comments or on Twitter with #LeetCode3592.”  

### 10. References & Further Reading

- Link to the LeetCode discussion thread.  
- Suggest similar problems (e.g., “Minimum Add to Make Parentheses Valid”, “Make Palindrome”).  
- Recommend “Cracking the Coding Interview” for deeper DP concepts.  

---

> **Closing Thought**  
> Mastering the reverse‑DP trick not only solves LeetCode 3592 but also equips you to tackle any interview problem that asks you to deduce hidden structures from outputs. Keep the intuition alive, practice the explanation, and you’ll turn a tricky puzzle into a showcase of your coding prowess. Happy hacking! 🚀

---

## 🎉 Final Words

You now have:

- A clean, proven algorithm to solve LeetCode 3592.  
- Three language‑agnostic implementations.  
- A ready‑to‑post SEO‑optimized blog outline to impress recruiters on LinkedIn or a personal portfolio.  

Happy coding and best of luck on your next interview!