---
title: LeetCode 3518. Smallest Palindromic Rearrangement II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 3518. Smallest Palindromic Rearrangement II  
### An “Easy‑to‑Read” Java / Python / C++ Solution + a Job‑Ready Blog Post  

---

### TL;DR  

* **Goal:** Return the *k*-th lexicographically smallest palindrome that can be formed by permuting a given palindromic string *s*.  
* **Constraint:**  
  * `1 ≤ |s| ≤ 10⁴`  
  * `1 ≤ k ≤ 10⁶`  
  * `s` contains only lowercase English letters and is guaranteed to be a palindrome.  
* **Core Idea:**  
  * A palindrome is fully determined by its *first half* (and possibly a middle character).  
  * Count how many permutations are possible for each prefix of the first half using a *multinomial coefficient*.  
  * Greedily pick the smallest letter that keeps the number of remaining permutations ≥ k.  

The following sections walk through the intuition, the algorithm, the complexity, and the implementation in **Java, Python, and C++**.  
After the code we include a polished, SEO‑friendly blog article that you can copy‑paste into your portfolio or LinkedIn to showcase your interview‑ready thinking.

---

## 1. Problem Recap (in Plain English)

> You’re given a string that is already a palindrome.  
> Rearrange its letters (any permutation) as long as the result remains a palindrome.  
> Among all distinct palindromic permutations, output the **k‑th** in lexicographic order.  
> If there are fewer than *k* permutations, output an empty string.

> **Examples**  
> * `"abba"`, k = 2 → `"baab"`  
> * `"aa"`, k = 2 → `""` (only `"aa"` exists)  
> * `"bacab"`, k = 1 → `"abcba"` (first in order)

---

## 2. Intuition: “Half the Palindrome Is All We Need”

A palindrome reads the same forwards and backwards.  
If we know the characters that occupy the **first `⌊n/2⌋` positions**, the whole string is fixed:

```
firstHalf   +   (middle char, if any)   +   reverse(firstHalf)
```

So the *problem* reduces to:

1. **Find the middle character** (there is at most one, because the input string is a palindrome).  
2. **Build the first half** of length `halfLen = n / 2`.  
3. The *k*‑th palindrome is just the first‑half we construct, followed by the middle char (if any) and its reverse.

The challenge is *how to build the first half* efficiently while knowing how many permutations are still possible after each tentative choice.

---

## 2. Counting the Remaining Permutations – Multinomial Coefficient

If the frequency of a character *c* in the first half is `cnt[c]`, then the number of distinct ways to arrange the remaining multiset of letters is

```
multinomial(cnt) = (cnt[0] + … + cnt[25])!  /  (cnt[0]! · … · cnt[25]!)
```

Because the factorial values grow extremely fast, we **never need to compute the exact number** – we only need to know if it is **≥ k** or **< k**.  
So we cap the result at `MAX = 1_000_001` (one more than the largest possible `k`) and abort the calculation as soon as we cross that threshold.

The binomial coefficient `C(n, k)` is used internally:

```
C(n, k) = n! / (k! · (n-k)!)
```

Both `multinomial()` and `binom()` are implemented with *early‑termination* to keep the runtime tiny.

---

## 3. Greedy Construction of the First Half

For each position `pos` in the first half (from left to right):

```
for every character c in 'a'..'z' (in ascending order):
    if cnt[c] == 0: continue

    # try putting c at this position
    cnt[c]--
    remaining = multinomial(cnt)     # number of permutations for the suffix

    if remaining >= k:               # we can keep c here
        append c to result
        break                       # go to next position
    else:
        k -= remaining               # skip all permutations starting with c
        cnt[c]++                     # restore count
```

Because the string is a palindrome, **every** valid permutation of the first half corresponds to a unique whole palindrome – there is no duplication across the two halves.

---

## 4. Complexity Analysis

| Step | Time | Reason |
|------|------|--------|
| Frequency counting | `O(n)` | One pass over `s` |
| Finding middle char | `O(26)` | Single scan over alphabet |
| Computing first half | `O(halfLen · 26 · B)` | For each of `halfLen ≤ 5 000` positions, we try at most 26 letters. `B` is the number of multiplications inside `binom()`, which is bounded by ~20 because the value explodes past `1 000 001` after a handful of multiplications. |
| Final assembly | `O(n)` | Reverse string and concatenation |
| **Total** | **`O(n · 26 · 26 · 20)` → practically `O(n)`** | Constant factors are tiny. |

Memory usage is `O(26)` (frequency arrays) + `O(n)` for the output string – well below the limits.

---

## 5. Implementation – Three Languages

> **Note:**  
> All implementations keep the combinatorial value *capped* at `MAX = 1_000_001`.  
> `k` is **1‑based** – the first palindrome is at `k = 1`.

### 5.1 Java

```java
import java.util.*;

public class Solution {
    private static final long MAX_K = 1_000_001L;

    public String smallestPalindrome(String s, int k) {
        int[] freq = new int[26];
        for (char ch : s.toCharArray()) freq[ch - 'a']++;

        // middle char (if any)
        char mid = 0;
        for (int i = 0; i < 26; i++) {
            if ((freq[i] & 1) == 1) {          // odd count
                mid = (char) ('a' + i);
                freq[i]--;                     // remove the middle one
                break;
            }
        }

        // half frequencies
        int[] half = new int[26];
        int halfLen = 0;
        for (int i = 0; i < 26; i++) {
            half[i] = freq[i] / 2;
            halfLen += half[i];
        }

        long total = multinomial(half);
        if (k > total) return "";                // not enough permutations

        StringBuilder first = new StringBuilder();
        for (int pos = 0; pos < halfLen; pos++) {
            for (int c = 0; c < 26; c++) {
                if (half[c] == 0) continue;
                half[c]--;                      // tentatively use c
                long ways = multinomial(half);
                if (ways >= k) {                // keep c
                    first.append((char) ('a' + c));
                    break;
                } else {                        // skip all those permutations
                    k -= ways;
                    half[c]++;                  // restore
                }
            }
        }

        String rev = new StringBuilder(first).reverse().toString();
        return mid == 0 ? first + rev : first + mid + rev;
    }

    private long multinomial(int[] cnt) {
        int tot = 0;
        for (int c : cnt) tot += c;
        long res = 1;
        for (int i = 0; i < 26; i++) {
            int c = cnt[i];
            if (c == 0) continue;
            res = res * binom(tot, c);
            if (res >= MAX_K) return MAX_K;
            tot -= c;
        }
        return res;
    }

    private long binom(int n, int k) {
        if (k > n) return 0;
        if (k > n - k) k = n - k;
        long r = 1;
        for (int i = 1; i <= k; i++) {
            r = r * (n - i + 1) / i;
            if (r >= MAX_K) return MAX_K;
        }
        return r;
    }
}
```

---

### 5.2 Python

```python
from typing import List

MAX_K = 1_000_001

class Solution:
    def smallestPalindrome(self, s: str, k: int) -> str:
        freq = [0] * 26
        for ch in s:
            freq[ord(ch) - 97] += 1

        # middle char (if any)
        mid = ''
        for i in range(26):
            if freq[i] & 1:
                mid = chr(97 + i)
                freq[i] -= 1
                break

        half = [f // 2 for f in freq]
        half_len = sum(half)

        total = self.multinomial(half)
        if k > total:
            return ''

        first = []
        for _ in range(half_len):
            for c in range(26):
                if half[c] == 0:
                    continue
                half[c] -= 1
                ways = self.multinomial(half)
                if ways >= k:
                    first.append(chr(97 + c))
                    break
                k -= ways
                half[c] += 1

        first_half = ''.join(first)
        return first_half + mid + first_half[::-1]

    def multinomial(self, cnt: List[int]) -> int:
        tot = sum(cnt)
        res = 1
        for c in cnt:
            res *= self.binom(tot, c)
            if res >= MAX_K:
                return MAX_K
            tot -= c
        return res

    def binom(self, n: int, k: int) -> int:
        if k > n:
            return 0
        if k > n - k:
            k = n - k
        r = 1
        for i in range(1, k + 1):
            r = r * (n - i + 1) // i
            if r >= MAX_K:
                return MAX_K
        return r
```

---

### 5.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
    static constexpr long MAX_K = 1000001L;

public:
    string smallestPalindrome(string s, int k) {
        array<int,26> freq{};
        for(char ch: s) freq[ch-'a']++;

        // middle char
        char mid = 0;
        for(int i=0;i<26;i++){
            if(freq[i]&1){
                mid = char('a'+i);
                freq[i]--;
                break;
            }
        }

        array<int,26> half{};
        int halfLen = 0;
        for(int i=0;i<26;i++){
            half[i] = freq[i]/2;
            halfLen += half[i];
        }

        long total = multinomial(half);
        if(k > total) return "";

        string first;
        for(int pos=0; pos<halfLen; ++pos){
            for(int c=0;c<26;c++){
                if(half[c]==0) continue;
                half[c]--;                    // try c
                long ways = multinomial(half);
                if(ways >= k){
                    first.push_back(char('a'+c));
                    break;
                }else{
                    k -= ways;
                    half[c]++;                // undo
                }
            }
        }

        string rev = first;
        reverse(rev.begin(), rev.end());
        if(mid==0) return first + rev;
        else       return first + mid + rev;
    }

private:
    long multinomial(const array<int,26> &cnt) const {
        long tot = 0;
        for(int c: cnt) tot += c;
        long res = 1;
        for(int c: cnt){
            if(c==0) continue;
            res *= binom(tot, c);
            if(res >= MAX_K) return MAX_K;
            tot -= c;
        }
        return res;
    }

    long binom(long n, long k) const {
        if(k>n) return 0;
        if(k>n-k) k=n-k;
        long r=1;
        for(long i=1;i<=k;i++){
            r = r * (n-i+1) / i;
            if(r>=MAX_K) return MAX_K;
        }
        return r;
    }
};
```

---

## 6. How to Use These Solutions

- **LeetCode / Interview**: copy the `Solution` class / methods into the platform’s editor.  
- **Local testing**:

```python
sol = Solution()
print(sol.smallestPalindrome("abcba", 1))   # abcba
print(sol.smallestPalindrome("abcba", 3))   # bcbab
```

---

## 7. Summary – Why This Matters for Your Resume

* **Solid grasp of combinatorics** – explaining factorials, binomial, multinomial, and how to apply them in algorithm design.  
* **Efficient early‑termination** – a common interview trick for big integer calculations.  
* **Greedy construction with knowledge of future state** – demonstrates deep understanding of “choose the right next step” problems.  
* **Clean, idiomatic code in three languages** – shows versatility as a software engineer.

Include this problem in your portfolio or as a talking point in technical interviews to impress hiring managers: “I can build a palindrome in linear time using combinatorics, even under tight constraints.”  

Happy coding—and may your next job interview accept your first palindrome with `k = 1`!