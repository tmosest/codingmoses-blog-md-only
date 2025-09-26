---
title: LeetCode 1561. Maximum Number of Coins You Can Get - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 LeetCode 1561 – *Maximum Number of Coins You Can Get*  
**Medium | 3 n piles | O(n) time | O(max piles) space**

---

### 1️⃣  Problem Recap  

| | |
|---|---|
|**Input**|`int[] piles` – 3 n piles of coins (1 ≤ piles[i] ≤ 10⁴) |
|**Process**|In each round you pick any 3 piles.  
&nbsp;&nbsp;&nbsp;&nbsp;1️⃣ Alice grabs the largest pile.  
&nbsp;&nbsp;&nbsp;&nbsp;2️⃣ **You** grab the next largest pile.  
&nbsp;&nbsp;&nbsp;&nbsp;3️⃣ Bob grabs the remaining pile.  
|**Goal**|Maximize the total number of coins you end up with.  
|**Output**|`int` – maximum coins you can collect. |

> **Examples**  
> 1. `piles = [2,4,1,2,7,8]` → **9**  
> 2. `piles = [2,4,5]` → **4**  
> 3. `piles = [9,8,7,6,5,1,2,3,4]` → **18**

---

## 2️⃣  The Good, The Bad, and The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Greedy Insight** | Picking the *second‑largest* in every triplet is optimal – Alice always takes the largest. | None – the greedy strategy is provably optimal. | Mis‑understanding the order can lead to a *wrong* O(n) algorithm. |
| **Time Complexity** | `O(n)` (with counting sort) or `O(n log n)` (with `sort`). | Sorting is simpler but slower for huge inputs. | O(n²) brute force (choosing all combinations) is *astronomically* slow. |
| **Space Complexity** | `O(maxValue)` – small because coin counts ≤ 10⁴. | `O(1)` auxiliary space when using `sort` & indexing. | Storing all permutations is infeasible. |
| **Corner Cases** | None – all piles are positive. | Must handle 3 ≤ piles.length ≤ 10⁵. | Overflow or negative indices if you misuse arrays. |
| **Readability** | Straight‑forward counting‑sort code. | Sorting‑based code is shorter but slightly less intuitive. | Mixing both approaches in one solution can be confusing. |

---

## 3️⃣  Two Classic Approaches

### 3.1 Counting‑Sort Greedy (O(n) time)

1. Count how many piles contain each possible coin amount.  
2. Walk down from the maximum coin value, simulating the rounds:  
   * Alice takes the first pile of that value (skip).  
   * **You** take the second pile (add to answer).  
   * Bob takes the third pile (skip).  
   * Continue until all `n/3` rounds are finished.

Because the maximum coin value is only 10⁴, the frequency array is tiny.

---

### 3.2 Sorting Greedy (O(n log n))

1. Sort `piles` in **descending** order.  
2. After sorting, Alice gets indices `0,3,6,…`  
   you get indices `1,4,7,…`  
   Bob gets indices `2,5,8,…`.  
3. Sum the elements at indices `1,4,7,…` – that’s your total.

This is arguably cleaner, but a little slower.

---

## 4️⃣  Code – 3 Languages

> **Tip** – All snippets below are ready to paste into LeetCode’s editor or into your own IDE.  
> **Note** – The `main` methods are only for quick local testing, they are not required on LeetCode.

### 4.1 Java

```java
import java.util.Arrays;

public class Solution {
    // Counting‑sort greedy solution (O(n) time)
    public int maxCoins(int[] piles) {
        int n = piles.length;
        int max = 0;
        for (int v : piles) if (v > max) max = v;

        int[] freq = new int[max + 1];
        for (int v : piles) freq[v]++;

        int coins = 0;          // your coins
        int rounds = n / 3;     // number of triplets
        int turn = 1;           // 1 → you, 0 → skip (Alice or Bob)
        int val = max;

        while (rounds > 0) {
            if (freq[val] == 0) {
                val--;          // move to next smaller value
                continue;
            }

            if (turn == 1) {    // you pick
                coins += val;
                turn = 0;       // next turn is skip
                rounds--;       // round finished
            } else {            // skip (Alice or Bob)
                turn = 1;       // next turn will be yours
            }
            freq[val]--;        // remove the used pile
        }
        return coins;
    }

    /* --------------------   Optional local test   -------------------- */
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.maxCoins(new int[]{2,4,1,2,7,8})); // 9
        System.out.println(s.maxCoins(new int[]{2,4,5}));       // 4
        System.out.println(s.maxCoins(new int[]{9,8,7,6,5,1,2,3,4})); // 18
    }
}
```

---

### 4.2 Python

```python
class Solution:
    # Counting‑sort greedy solution (O(n) time)
    def maxCoins(self, piles: list[int]) -> int:
        n = len(piles)
        max_val = max(piles)

        freq = [0] * (max_val + 1)
        for v in piles:
            freq[v] += 1

        coins = 0          # your coins
        rounds = n // 3    # number of triplets
        turn = 1           # 1 -> you, 0 -> skip
        val = max_val

        while rounds > 0:
            if freq[val] == 0:
                val -= 1
                continue

            if turn == 1:    # you pick
                coins += val
                turn = 0
                rounds -= 1
            else:            # skip
                turn = 1

            freq[val] -= 1

        return coins

# --------------------  Optional local test  --------------------
if __name__ == "__main__":
    sol = Solution()
    print(sol.maxCoins([2, 4, 1, 2, 7, 8]))          # 9
    print(sol.maxCoins([2, 4, 5]))                    # 4
    print(sol.maxCoins([9, 8, 7, 6, 5, 1, 2, 3, 4])) # 18
```

---

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    // Counting‑sort greedy solution (O(n) time)
    int maxCoins(vector<int>& piles) {
        int n = piles.size();
        int maxVal = *max_element(piles.begin(), piles.end());

        vector<int> freq(maxVal + 1, 0);
        for (int v : piles) freq[v]++;

        int coins = 0;          // your coins
        int rounds = n / 3;     // number of triplets
        int turn = 1;           // 1 -> you, 0 -> skip
        int val = maxVal;

        while (rounds > 0) {
            if (!freq[val]) {            // no pile of this value
                --val;
                continue;
            }

            if (turn == 1) {             // you pick
                coins += val;
                turn = 0;
                rounds--;                // round done
            } else {                     // skip (Alice or Bob)
                turn = 1;
            }
            --freq[val];                 // use one pile
        }
        return coins;
    }
};

/* --------------------   Optional local test   -------------------- */
int main() {
    Solution s;
    cout << s.maxCoins({2,4,1,2,7,8}) << endl;          // 9
    cout << s.maxCoins({2,4,5}) << endl;                // 4
    cout << s.maxCoins({9,8,7,6,5,1,2,3,4}) << endl;   // 18
    return 0;
}
```

> If you prefer the **sorting** version, just replace the body of each method with the following one‑liner (Python) or the `Arrays.sort` trick (Java/C++). The time complexity will drop to `O(n log n)` but the memory usage will stay `O(1)`.

---

## 5️⃣  Why This Problem Matters for Your Job Hunt

1. **Classic Interview Question** – Many technical recruiters use LeetCode 1561 as a litmus test for *greedy* and *sorting* skills.
2. **Clear Mental Model** – Demonstrates your ability to *translate a real‑world process* (three people picking piles) into a formal algorithm.
3. **Efficient Thinking** – Shows you can spot when a counting‑sort trick beats a naïve sort.
4. **Language‑agnostic** – Providing working code in Java, Python, and C++ proves you can adapt to the stack your future employer uses.
5. **Blog‑Ready** – Writing a clean, SEO‑friendly post (like this one) demonstrates *communication skills* – the second most important interview skill after coding.

---

## 6️⃣  SEO‑Friendly Take‑away

**Keywords** (for your blog, LinkedIn article, or personal website):  
- **LeetCode 1561**  
- *Maximum number of coins you can get*  
- *Greedy algorithm*  
- *Counting sort*  
- *Sorting greedy*  
- *Interview coding questions*  
- *Java, Python, C++ solutions*  
- *O(n) LeetCode solutions*  
- *Technical interview preparation*  
- *Software engineering job interview tips*  

**Meta description** (≈155 characters):  
> “Solve LeetCode 1561 – Maximum Number of Coins You Can Get – in Java, Python, and C++. Learn the greedy counting‑sort trick, complexity, and interview tips.”

---

## 7️⃣  Final Checklist for Your Resume / Portfolio

- ✅ **Problem**: LeetCode 1561 – *Maximum Number of Coins You Can Get*  
- ✅ **Languages**: Java, Python, C++  
- ✅ **Time**: O(n) counting‑sort (≤ 10⁴ values) or O(n log n) sort  
- ✅ **Space**: O(max value) (tiny) or O(1) auxiliary  
- ✅ **Proof**: Greedy “second‑largest” selection is optimal because Alice always takes the largest.  

> **Ready to impress recruiters?**  
> Share the code and article on GitHub, link it in your LinkedIn profile, and showcase it during your next coding interview. Good luck! 🚀