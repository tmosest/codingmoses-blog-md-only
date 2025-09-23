---
title: LeetCode 2484. Count Palindromic Subsequences - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution for “Count Palindromic Subsequences of Length 5”**  
(LeetCode 2466 – “Count Palindromic Subsequences of Length 5”)

--------------------------------------------------------------------

### 1. Problem restatement

You are given a string **`s`** (`0 ≤ s[i] ≤ '9'`) of length `n (1 ≤ n ≤ 2000)`.  
A *subsequence* is obtained by deleting any characters (possibly none) while keeping the relative order.

A subsequence of length 5 is a **palindrome** if it reads the same forwards and backwards,  
i.e. for the chosen indices `i1 < i2 < i3 < i4 < i5`

```
s[i1] == s[i5]   and   s[i2] == s[i4]   (the middle element i3 is irrelevant)
```

Return the number of such palindromic subsequences modulo `1 000 000 007`.

--------------------------------------------------------------------

### 2. Observations

* The string consists only of **10** different digits (`0`–`9`).  
  This small alphabet allows us to count “patterns” rather than positions.

* For a fixed middle element `s[k]` (the 3‑rd position of the palindrome)  
  we only need to know:

  * how many **pairs** `(a, b)` (`a` before `b`) occur **to the left** of `k`, and  
  * how many pairs `(b, a)` occur **to the right** of `k`.

  Then each pair from the left can be matched with a pair from the right to form a
  palindrome `a b _ b a`.

* Counting pairs can be done in a single left‑to‑right scan and a right‑to‑left scan
  while keeping a frequency array for the characters seen so far.

--------------------------------------------------------------------

### 3. Algorithm (Prefix–Suffix + Rolling DP)

The following algorithm is O(`n · 10²`) time and O(`10²`) additional memory.

```
MOD = 1_000_000_007

digits = s.toCharArray()
n      = digits.length
if n < 5 return 0

// 1. Build suffix pair counts (pairs that will be on the right side of the center)
freqRight = int[10]              // how many of each digit are still on the right
rightPairs = int[10][10]         // rightPairs[x][y] = # of x (first) followed by y (second) in the suffix

for i from n-1 downto 0:
    cur = digits[i] - '0'
    for a in 0..9:
        rightPairs[cur][a] += freqRight[a]          // pair (cur, a) with cur coming before a
    freqRight[cur] += 1

// 2. Sweep through every possible middle position
freqLeft  = int[10]
leftPairs = int[10][10]   // leftPairs[a][b] = # of a (first) followed by b (second) on the left

answer = 0
for i from 0 to n-1:
    cur = digits[i] - '0'

    // remove the current character from the right side
    freqRight[cur] -= 1
    for a in 0..9:
        rightPairs[cur][a] -= freqRight[a]          // all pairs that involved this `cur` are no longer on the right

    // accumulate all palindromes whose centre is i
    for a in 0..9:
        for b in 0..9:
            answer += (long)leftPairs[a][b] * rightPairs[b][a]   // pair (a,b) left ↔ pair (b,a) right
            answer %= MOD

    // add current character to left side
    for a in 0..9:
        leftPairs[a][cur] += freqLeft[a]             // pair (a, cur) with a coming before cur
    freqLeft[cur] += 1

return (int)answer
```

--------------------------------------------------------------------

### 4. Correctness Proof  

We prove that the algorithm returns the exact number of length‑5 palindromic
subsequences.

---

#### Lemma 1  
During the first loop (`i` from `n-1` to `0`)  
`rightPairs[x][y]` equals the number of pairs of indices `(p, q)` such that

```
p < q ,   p ≥ i   (both are in the suffix starting at i)
s[p] = 'x', s[q] = 'y'
```

**Proof.**  
When we process position `i` (character `cur`), `freqRight[a]` counts how many `a`'s
appear **after** position `i`.  
Each of those positions forms a pair `(cur, a)` with `cur` preceding `a`.  
Thus we add `freqRight[a]` to `rightPairs[cur][a]`.  
Afterwards we mark `cur` as “seen on the right” by incrementing `freqRight[cur]`.  
Induction over `i` gives the claim. ∎



#### Lemma 2  
After the second loop starts the iteration for index `i`,
`leftPairs[a][b]` equals the number of index pairs `(p, q)` such that

```
p < q ,   q < i   (both are on the left side of the centre)
s[p] = 'a', s[q] = 'b'
```

**Proof.**  
`leftPairs` is updated only in the current iteration (`i`).
Before adding the pair involving the current character `cur` the array
contains exactly all pairs that end **before** position `i`.  
When we finish processing `i`, we add all pairs `(a, cur)` where `a` appeared earlier on the left.
Hence before the next iteration (`i+1`) the array contains all pairs with `q ≤ i`. ∎



#### Lemma 3  
For a fixed middle position `k` (the current `cur` in the sweep),
the algorithm adds to the answer exactly the number of palindromes of the form

```
a b _ b a   where s[k] is the middle element
```

**Proof.**  
A palindrome `a b _ b a` requires a left pair `(a,b)` and a right pair `(b,a)`.  
By Lemma&nbsp;1, after the right sweep `rightPairs[b][a]` counts the number of such
right pairs that will appear *after* the middle position.  
By Lemma&nbsp;2, `leftPairs[a][b]` counts the number of left pairs that appear *before* the middle.  
Multiplying the two numbers counts all ways to choose one left pair and one right pair,
which is precisely the number of palindromes whose centre is at index `k`.  
The algorithm sums this product over all `a,b` and over all valid centre indices,
hence each palindrome of length 5 is counted exactly once. ∎



#### Theorem  
The algorithm returns the number of palindromic subsequences of length 5 in `s`,
modulo `1 000 000 007`.

**Proof.**  
*Existence:*  
Any palindrome `a b _ b a` has a distinct middle index `k`.  
By Lemma&nbsp;3 it is counted once when the sweep reaches `k`.

*Uniqueness:*  
No other combination of indices is added to the answer – the inner loops only
combine a left pair with a right pair that have the same pattern `(a,b)` and `(b,a)`.  
Therefore each counted subsequence is a palindrome of length 5.

*Completeness:*  
All palindromic subsequences of length 5 have the described pattern
`a b _ b a`; thus they are considered by the algorithm.

Hence the algorithm counts exactly all desired subsequences. ∎



--------------------------------------------------------------------

### 4. Complexity Analysis

| Step | Time | Extra Memory |
|------|------|--------------|
| Build suffix pair counts | `O(n · 10)` | `O(10)` (freqRight) + `O(10²)` (rightPairs) |
| Sweep over centres | `O(n · 10²)` | `O(10)` (freqLeft) + `O(10²)` (leftPairs) |
| Final summation | `O(n · 10²)` | — |
| **Total** | **`O(n · 10²)` ≈ 4 · 10⁶ operations** | **`O(10²)` ≈ 400 integers** |

With `n ≤ 2000` the program performs at most about **20 million** elementary
operations, easily below the 2 s limit.  
Memory consumption is far below the 256 MB limit.

--------------------------------------------------------------------

### 5. Code

Below you can find a ready‑to‑paste implementation for the three required
languages.

---

#### Java 17

```java
import java.util.*;

public class Solution {
    private static final int MOD = 1_000_000_007;

    public int countPalindromes(String s) {
        int n = s.length();
        if (n < 5) return 0;

        char[] digits = s.toCharArray();

        // ----- suffix pair counts (right side of the centre) -----
        int[] freqRight = new int[10];
        int[][] rightPairs = new int[10][10];   // rightPairs[x][y] = # of pair (x, y) in the suffix

        for (int i = n - 1; i >= 0; --i) {
            int cur = digits[i] - '0';
            for (int a = 0; a < 10; ++a) {
                rightPairs[cur][a] += freqRight[a];
            }
            freqRight[cur] += 1;
        }

        // ----- sweep over every possible middle position -----
        int[] freqLeft = new int[10];
        int[][] leftPairs = new int[10][10];   // leftPairs[a][b] = # of pair (a, b) on the left

        long ans = 0;
        for (int i = 0; i < n; ++i) {
            int cur = digits[i] - '0';

            // Remove current element from the right side
            freqRight[cur] -= 1;
            for (int a = 0; a < 10; ++a) {
                // subtract all pairs that involved this 'cur' as the first element
                // (the number of pairs (cur, a) that will no longer exist)
                // We keep everything in long during accumulation, so a simple subtraction works
                rightPairs[cur][a] -= freqRight[a];
                if (rightPairs[cur][a] < 0) rightPairs[cur][a] += MOD; // keep it non‑negative modulo
            }

            // Add contribution of this centre
            for (int a = 0; a < 10; ++a) {
                for (int b = 0; b < 10; ++b) {
                    ans += (long) leftPairs[a][b] * rightPairs[b][a];
                    ans %= MOD;
                }
            }

            // Insert current element into the left side
            for (int a = 0; a < 10; ++a) {
                leftPairs[a][cur] += freqLeft[a];
                leftPairs[a][cur] %= MOD;
            }
            freqLeft[cur] += 1;
        }

        return (int) (ans % MOD);
    }
}
```

---

#### C++17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    static constexpr int MOD = 1'000'000'007;

    int countPalindromes(string s) {
        int n = s.size();
        if (n < 5) return 0;

        // suffix pair counts
        vector<int> freqRight(10, 0);
        array<array<int,10>,10> rightPairs{};
        for (int i = n - 1; i >= 0; --i) {
            int cur = s[i] - '0';
            for (int a = 0; a < 10; ++a)
                rightPairs[cur][a] += freqRight[a];
            ++freqRight[cur];
        }

        // sweep over the centre
        vector<int> freqLeft(10, 0);
        array<array<int,10>,10> leftPairs{};
        long long ans = 0;
        for (int i = 0; i < n; ++i) {
            int cur = s[i] - '0';

            // remove cur from the right side
            --freqRight[cur];
            for (int a = 0; a < 10; ++a) {
                rightPairs[cur][a] -= freqRight[a];
                if (rightPairs[cur][a] < 0) rightPairs[cur][a] += MOD; // keep positive mod
            }

            // add contributions
            for (int a = 0; a < 10; ++a)
                for (int b = 0; b < 10; ++b) {
                    ans += 1LL * leftPairs[a][b] * rightPairs[b][a];
                    ans %= MOD;
                }

            // add cur to left side
            for (int a = 0; a < 10; ++a) {
                leftPairs[a][cur] += freqLeft[a];
                leftPairs[a][cur] %= MOD;
            }
            ++freqLeft[cur];
        }

        return static_cast<int>(ans % MOD);
    }
};
```

---

#### Python 3

```python
class Solution:
    MOD = 1_000_000_007

    def countPalindromes(self, s: str) -> int:
        n = len(s)
        if n < 5:
            return 0

        digits = [ord(ch) - 48 for ch in s]          # convert to 0‑9

        # suffix pair counts
        freq_right = [0] * 10
        right_pairs = [[0] * 10 for _ in range(10)]
        for cur in reversed(digits):
            for a in range(10):
                right_pairs[cur][a] += freq_right[a]
            freq_right[cur] += 1

        # sweep over centre
        freq_left = [0] * 10
        left_pairs = [[0] * 10 for _ in range(10)]
        ans = 0
        for cur in digits:
            # remove cur from right side
            freq_right[cur] -= 1
            for a in range(10):
                right_pairs[cur][a] -= freq_right[a]
                if right_pairs[cur][a] < 0:
                    right_pairs[cur][a] += self.MOD

            # add contribution of this centre
            for a in range(10):
                for b in range(10):
                    ans += left_pairs[a][b] * right_pairs[b][a]
                    ans %= self.MOD

            # insert cur into left side
            for a in range(10):
                left_pairs[a][cur] += freq_left[a]
                left_pairs[a][cur] %= self.MOD
            freq_left[cur] += 1

        return ans % self.MOD
```

---

#### How to Run

* **Java** – paste the `Solution` class into the LeetCode editor (or your own test harness).  
* **C++** – compile with `g++ -std=c++17 -O2 -pipe -static -s main.cpp` and run.  
* **Python** – create a `Solution` object and call `countPalindromes("…")`.

--------------------------------------------------------------------

### 6. What if we want to actually list the subsequences?

The algorithm counts them, but printing every subsequence would be
exponential in the answer size, and not required by the problem statement.
If you truly need to enumerate all subsequences, you can modify the
inner loops to store the chosen indices instead of the product of counts.
However, the total number of palindromes can be enormous (up to \( \binom{2000}{5} \)),
so the output would be astronomically large.

--------------------------------------------------------------------

### 7. Summary

* The problem is to count, not to output, length‑5 palindromic subsequences.  
* By observing that such a subsequence must have the form `a b _ b a`,
  we can reduce the counting to counting pairs of indices on each side of the centre.
* Two linear passes (one for the suffix, one for the sweep) give us all pair
  frequencies efficiently.
* Combining the two sides for every possible centre yields the exact count.
* The solution runs in \(O(n\cdot10^2)\) time and uses only \(O(10^2)\) memory.

Feel free to use any of the snippets above as part of your own projects!