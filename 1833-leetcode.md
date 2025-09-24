---
title: LeetCode 1833. Maximum Ice Cream Bars - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Solution Overview (Counting‑Sort Approach)

| Language | Time Complexity | Space Complexity | Why it works |
|----------|-----------------|------------------|--------------|
| **Java** | `O(n + C)` | `O(C)` | `C = max(cost)` ≤ 10⁵ |
| **Python** | `O(n + C)` | `O(C)` | Uses a `list` of length `max_cost+1` |
| **C++** | `O(n + C)` | `O(C)` | Uses a `vector<int>` of length `max_cost+1` |

*`n`* – number of ice‑cream bars (≤ 10⁵)  
*`C`* – maximum cost in the array (≤ 10⁵)

Because the prices are bounded by 10⁵ we can skip the usual `O(n log n)` sort and simply count how many bars have each price.  
Then we walk from the cheapest price to the most expensive, buying as many bars as our remaining coins allow.

---

## 2.  Code

Below are three self‑contained implementations that can be copied into a LeetCode “New Solution” block.

---

### 2.1 Java

```java
// 1833. Maximum Ice Cream Bars – Counting Sort (Java)
class Solution {
    public int maxIceCream(int[] costs, int coins) {
        // 1️⃣ Find the maximum cost to size the counting array
        int maxCost = 0;
        for (int c : costs) maxCost = Math.max(maxCost, c);

        // 2️⃣ Count how many bars have each cost
        int[] freq = new int[maxCost + 1];
        for (int c : costs) freq[c]++;

        // 3️⃣ Buy bars from cheapest to most expensive
        int bars = 0;
        for (int price = 1; price <= maxCost; price++) {
            int available = freq[price];
            if (available == 0) continue;

            int canBuy = Math.min(available, coins / price);
            bars += canBuy;
            coins -= canBuy * price;

            // Optional early exit: no coins left for the next price
            if (coins < price) break;
        }
        return bars;
    }
}
```

---

### 2.2 Python

```python
# 1833. Maximum Ice Cream Bars – Counting Sort (Python 3)

class Solution:
    def maxIceCream(self, costs: List[int], coins: int) -> int:
        # 1️⃣ Determine the upper bound for the counting array
        max_cost = max(costs)

        # 2️⃣ Build frequency array
        freq = [0] * (max_cost + 1)
        for c in costs:
            freq[c] += 1

        # 3️⃣ Greedily buy from cheapest to most expensive
        bars = 0
        for price in range(1, max_cost + 1):
            if freq[price] == 0:
                continue
            can_buy = min(freq[price], coins // price)
            bars += can_buy
            coins -= can_buy * price
            if coins < price:
                break
        return bars
```

---

### 2.3 C++

```cpp
// 1833. Maximum Ice Cream Bars – Counting Sort (C++17)
class Solution {
public:
    int maxIceCream(vector<int>& costs, int coins) {
        // 1️⃣ Find the maximum cost
        int maxCost = *max_element(costs.begin(), costs.end());

        // 2️⃣ Frequency array
        vector<int> freq(maxCost + 1, 0);
        for (int c : costs) ++freq[c];

        // 3️⃣ Greedy purchase
        int bars = 0;
        for (int price = 1; price <= maxCost; ++price) {
            if (!freq[price]) continue;
            int canBuy = min(freq[price], coins / price);
            bars += canBuy;
            coins -= canBuy * price;
            if (coins < price) break;
        }
        return bars;
    }
};
```

---

## 3.  Blog Article – “The Good, the Bad, and the Ugly of Counting Sort on LeetCode”

### Title (SEO‑friendly)
**Counting‑Sort on LeetCode: The Good, the Bad, and the Ugly – A Job‑Ready Guide to 1833. Maximum Ice Cream Bars**

---

### Meta Description
Learn how to solve LeetCode 1833 “Maximum Ice Cream Bars” using counting sort. Step‑by‑step guide, Java/Python/C++ code, performance analysis, pitfalls, and interview tips.

---

### 1️⃣ Introduction  

Summer heat, a hungry kid, and a wallet full of coins – that’s the story behind LeetCode 1833. It sounds like a simple greedy problem, but the key twist is the *bounded* price range (1 ≤ cost ≤ 10⁵).  
This constraint invites a counting‑sort solution that beats the usual `O(n log n)` approach. In this post we dissect the **good**, the **bad**, and the **ugly** aspects of this method, give you production‑ready code in three languages, and show how mastering it can impress hiring managers.

---

### 2️⃣ Problem Recap

> You are given an array `costs` of size `n` (1 ≤ n ≤ 10⁵) and an integer `coins` (1 ≤ coins ≤ 10⁸).  
> Each element `costs[i]` (1 ≤ costs[i] ≤ 10⁵) is the price of the *i*‑th ice‑cream bar.  
> The boy can buy bars in any order and wants to buy as many as possible.  
> Return the maximum number of bars that can be bought with the available coins.

---

### 3️⃣ The Good – Why Counting Sort Rocks

| Feature | Benefit |
|---------|---------|
| **Linear Time** – `O(n + C)` | With `C = 10⁵`, the runtime is essentially linear, no log factor. |
| **Memory Predictable** – `O(C)` | Fixed size array of 100 001 integers (≈ 400 KB). |
| **Deterministic Complexity** | Avoids the worst‑case behaviour of quicksort (e.g., already sorted data). |
| **Clear Intuition** | Count frequencies, then sweep from cheapest to most expensive. |
| **Easy to Verify** | Debugging is trivial: you can print the frequency array to confirm counts. |

---

### 4️⃣ The Bad – When Counting Sort Might Fail

| Issue | Why it matters |
|-------|----------------|
| **Large Cost Range** | If the maximum cost were, say, 10⁹, the frequency array would be impossible to allocate. |
| **Sparse Data** | When costs are spread thinly across a huge range, counting sort turns into a waste of memory. |
| **Cache Unfriendly** | The frequency array can be larger than cache lines, potentially causing more misses than an `O(n log n)` sort on small inputs. |
| **Code Boilerplate** | You have to manually find `maxCost` and build the frequency array, adding a few lines of code. |

> In LeetCode 1833 the constraints guarantee the “good” scenario, so counting sort is the recommended approach.

---

### 5️⃣ The Ugly – Common Pitfalls & How to Avoid Them

1. **Off‑By‑One Errors**  
   *Solution:* Always size the frequency array as `maxCost + 1` and iterate from `1` to `maxCost` inclusive.

2. **Integer Overflow in Coin Subtraction**  
   *Solution:* Use `long` or `int64_t` for the intermediate product `canBuy * price` when the coins value can be up to 10⁸. In Java, `int` is safe because `maxCost ≤ 10⁵` and `coins ≤ 10⁸` → product ≤ 10¹³, which fits in `long`.

3. **Skipping Zero Frequencies**  
   *Solution:* Add a guard `if (freq[price] == 0) continue;` to avoid unnecessary divisions.

4. **Early Exit Conditions**  
   *Solution:* After each purchase, if `coins < price` then break. This avoids unnecessary loops when you’re out of money.

5. **Testing Edge Cases**  
   - All costs equal the maximum (`10⁵`).  
   - Coins less than the cheapest cost.  
   - Coins exactly enough for a subset of bars.

---

### 6️⃣ Implementation Walkthrough (Java)

1. **Find the maximum cost** – needed to know the array size.  
2. **Build frequency array** – one pass over `costs`.  
3. **Greedy sweep** – for each price from 1 to `maxCost`, buy as many as possible until coins run out.

The Java code in section **2.1** follows this exact plan and can be dropped straight into LeetCode.

---

### 7️⃣ Python & C++ Versions

The same logic applies; only syntax differs:

- **Python**: `freq = [0] * (max_cost + 1)`  
- **C++**: `vector<int> freq(maxCost + 1, 0)`

Both versions mirror the Java implementation and respect the same time/space complexity guarantees.

---

### 8️⃣ How to Use This Knowledge in Interviews

1. **Explain the Constraints**  
   Highlight that `costs[i]` ≤ 10⁵ → a counting array is feasible.

2. **State the Complexity**  
   “O(n + C) time, O(C) space. For this problem C = 10⁵, so it’s linear.”

3. **Show the Algorithm Flow**  
   Use a whiteboard diagram: “count → buy → repeat”.

4. **Discuss Edge Cases**  
   Ask the interviewer if they want you to handle negative costs or zero coins, then adapt.

5. **Mention Trade‑offs**  
   “If costs were unbounded, we would revert to sorting or a priority queue.”

---

### 9️⃣ Bonus: Mini‑Checklist for Your Next LeetCode Job

| ✅ | Item |
|---|------|
| ✔️ | Master both greedy and counting‑sort paradigms. |
| ✔️ | Practice writing clean, commented code in at least 2 languages. |
| ✔️ | Prepare to discuss time/space trade‑offs in interviews. |
| ✔️ | Keep a repository of LeetCode solutions with clear explanations. |
| ✔️ | Use the “Good, Bad, Ugly” framework to explain algorithms during technical screens. |

---

### 🔚 Conclusion

Counting sort is a simple, fast, and reliable technique when the key values are bounded – exactly the case for LeetCode 1833. By understanding the **good** (speed, simplicity), **bad** (memory limits, range constraints), and **ugly** (common bugs), you’ll be able to solve this problem flawlessly and impress interviewers with your algorithmic insight.

Happy coding, and may your ice‑cream bars always be the most affordable ones! 🍦

---

### 📌 SEO Keywords

- Counting Sort LeetCode
- 1833 Maximum Ice Cream Bars solution
- Java Python C++ greedy algorithm
- LeetCode interview tips
- Linear time algorithm
- Job interview algorithm explanation

--- 

*Feel free to share this article on LinkedIn, Reddit, or your personal blog – it’s packed with interview‑ready content and will help you climb the job search ladder.*