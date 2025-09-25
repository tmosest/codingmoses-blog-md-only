---
title: LeetCode 1561. Maximum Number of Coins You Can Get - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## ✅ 1561 – Maximum Number of Coins You Can Get  
**Problem | Medium | LeetCode**  

> There are `3n` piles of coins.  
> In each round you pick any three piles (they don’t have to be consecutive).  
> Alice takes the pile with the **most** coins, **you** take the second‑largest, Bob takes the last.  
> Repeat until no piles remain.  
> Return the maximum number of coins you can obtain.

---

### 📌 Key Insights

| Good | Bad | Ugly |
|------|-----|------|
| **Greedy** – we only care about the second‑largest pile in each 3‑tuple. | Choosing the “right” three piles each step is not obvious. | Brute‑forcing all permutations is infeasible (`O((3n)!)`). |
| **Counting Sort** – coin values ≤ `10⁴`, so a frequency array works in `O(maxVal + n)`. | Using a frequency array requires `O(maxVal)` memory, but `maxVal` is only `10⁴`. | Sorting the whole array (`O(n log n)`) is simpler but less efficient for very large `n`. |
| **Turn Simulation** – we alternate turns, skipping Alice’s turns to only add coins on our turn. | The code can become confusing if you forget to decrement the turn counter. | Off‑by‑one errors when moving the index downwards. |

---

## 🚀 The Final Solution – Counting Sort + Turn Simulation

1. Build a frequency array `freq[value] = how many piles contain `value`.
2. Start from the largest possible value and walk downwards.
3. Every time we encounter a pile:
   * If it’s Alice’s turn → skip (she takes it).
   * If it’s our turn → add the value to our total.
   * Always decrement the frequency of that value.
4. Repeat until we have taken `n = piles.length / 3` turns (i.e., we’ve chosen `n` piles).

This yields a time complexity of **O(n + maxVal)** and a space complexity of **O(maxVal)**, which satisfies the constraints.

---

## 📦 Code Snippets

Below are clean, idiomatic implementations in **Java**, **Python**, and **C++**.  
All three use the same algorithmic idea.

### 1️⃣ Java

```java
class Solution {
    public int maxCoins(int[] piles) {
        // Find the maximum pile size
        int max = 0;
        for (int p : piles) if (p > max) max = p;

        // Frequency array
        int[] freq = new int[max + 1];
        for (int p : piles) freq[p]++;

        int total = 0;                  // Coins you collect
        int rounds = piles.length / 3;   // Number of times you get to pick
        boolean isYourTurn = true;      // You always pick second

        for (int value = max; rounds > 0; value--) {
            while (freq[value] > 0 && rounds > 0) {
                if (isYourTurn) {
                    total += value;   // You take it
                }
                // Toggle turn
                isYourTurn = !isYourTurn;

                // If it was your pick, we consumed a round
                if (!isYourTurn) rounds--;

                freq[value]--;        // Remove the pile
            }
        }
        return total;
    }
}
```

### 2️⃣ Python

```python
class Solution:
    def maxCoins(self, piles: List[int]) -> int:
        max_val = max(piles)
        freq = [0] * (max_val + 1)

        for p in piles:
            freq[p] += 1

        total = 0
        rounds = len(piles) // 3
        turn = 1  # 1 => our turn, 0 => Alice's turn
        val = max_val

        while rounds:
            if freq[val]:
                if turn == 1:
                    total += val
                turn = 1 - turn  # switch turns
                if turn == 0:    # we just picked
                    rounds -= 1
                freq[val] -= 1
            else:
                val -= 1
        return total
```

### 3️⃣ C++

```cpp
class Solution {
public:
    int maxCoins(vector<int>& piles) {
        int maxVal = *max_element(piles.begin(), piles.end());
        vector<int> freq(maxVal + 1, 0);
        for (int p : piles) ++freq[p];

        int total = 0;
        int rounds = piles.size() / 3;
        bool isYourTurn = true;  // you pick second

        for (int val = maxVal; rounds; --val) {
            while (freq[val] && rounds) {
                if (isYourTurn) total += val;
                isYourTurn = !isYourTurn;
                if (!isYourTurn) --rounds;  // you just took
                --freq[val];
            }
        }
        return total;
    }
};
```

> **Tip** – All three solutions share the same `O(n + maxVal)` time and `O(maxVal)` space.  
> In practice, the `maxVal` is at most `10⁴`, so the memory usage is negligible.

---

## 📈 Complexity Analysis

| Metric | Java | Python | C++ |
|--------|------|--------|-----|
| Time   | `O(n + maxVal)` | `O(n + maxVal)` | `O(n + maxVal)` |
| Space  | `O(maxVal)` | `O(maxVal)` | `O(maxVal)` |
| Why?  | Single pass to build frequency array + linear scan downwards. | Same. | Same. |

Because `maxVal` is bounded by `10⁴`, this is essentially linear in `n` and far faster than an `O(n log n)` sort for large inputs.

---

## 🎯 How to Use This in an Interview

1. **Explain the greedy property** – you only care about the second‑largest pile in each triplet, because Alice always takes the largest.
2. **Justify counting sort** – show that coin values are small, so a frequency array is efficient.
3. **Walk through the turn logic** – demonstrate how you skip Alice’s picks and accumulate coins on your turn.
4. **Mention edge cases** – equal piles, very small `n`, or all piles of the same size. The algorithm handles them gracefully.
5. **Show test examples** – use the provided sample inputs and any custom tests (e.g., `piles = [1,1,1,1,1,1]` → output `2`).

---

## 📚 Quick Self‑Test

```python
def test():
    sol = Solution()
    assert sol.maxCoins([2,4,1,2,7,8]) == 9
    assert sol.maxCoins([2,4,5]) == 4
    assert sol.maxCoins([9,8,7,6,5,1,2,3,4]) == 18
    assert sol.maxCoins([1,1,1,1,1,1]) == 2
    print("All tests passed.")
```

Run the same tests in Java and C++ to be absolutely sure.

---

## 📣 SEO‑Friendly Blog Hook

> **LeetCode 1561** – “Maximum Number of Coins You Can Get” – a classic greedy problem solved with **counting sort**.  
> If you’re prepping for a technical interview or looking to land a software‑engineering role, mastering this problem shows you can apply **greedy algorithms**, **linear‑time sorting**, and write clean **Java / Python / C++** code.  
> Use the tags “LeetCode”, “algorithm”, “greedy”, “counting sort”, “Python interview”, “Java coding interview”, and “C++ algorithm” in your résumé and portfolio to boost visibility.

---

## 🏁 Final Takeaway

- **Greedy + Counting Sort** is the most efficient way to solve LeetCode 1561.  
- All three languages implement the same linear algorithm with negligible memory overhead.  
- Understanding the *turn simulation* part is the trickiest but essential skill for interviewers to appreciate.  

Good luck with your interview prep! 🚀