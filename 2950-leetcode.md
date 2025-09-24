---
title: LeetCode 2950. Number of Divisible Substrings - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 2950 – Number of Divisible Substrings  
**Problem type:** Medium  
**Time limit:** 2 s (≈ O(9 · n))  
**Space limit:** 256 MB  

---

### 1. Problem statement

> Each lower‑case English letter is mapped to a digit (see the table below).  
> A **substring** of a string is any contiguous, non‑empty sequence of characters.  
> A substring is **divisible** if the sum of the mapped values of its characters is divisible by its length.  
> Given a string `word` (1 ≤ |word| ≤ 2000), return the number of divisible substrings of `word`.

| Letter | Value |
|--------|-------|
| a, b   | 1 |
| c, d, e | 2 |
| f, g, h | 3 |
| i, j, k | 4 |
| l, m, n | 5 |
| o, p, q | 6 |
| r, s, t | 7 |
| u, v, w | 8 |
| x, y, z | 9 |

*Examples*

| `word` | answer | reasoning |
|--------|--------|-----------|
| `asdf` | 6 | see the sample table in the problem statement |
| `bdh`  | 4 | `"b"`, `"d"`, `"h"`, `"bdh"` |
| `abcd` | 6 | `"a"`, `"b"`, `"c"`, `"d"`, `"ab"`, `"cd"` |

---

### 2. Intuition – “average = integer”

For a substring of length `p` let its mapped values be `f(c1)…f(cp)`.  
The substring is divisible iff

```
   f(c1)+f(c2)+…+f(cp)
= ─────────────────────  is an integer
          p
```

Thus the **average value** of the substring must be an integer between 1 and 9.

If we fix an average `a` (1 ≤ a ≤ 9) we can ask: *how many substrings have average exactly `a`?*

For a given `a`

```
f(c1)+…+f(cp) = p · a
⇔ (f(c1)-a) + … + (f(cp)-a) = 0
```

So the problem reduces to counting sub‑arrays whose **transformed sum is zero**.  
That can be done in linear time with a prefix‑sum + hash‑map trick.

Because there are only 9 possible averages we simply repeat the linear scan 9 times – **O(9 · n)** overall.

---

### 3. Algorithm (exact steps)

1. **Pre‑compute the mapping**  
   `int val[26] = {1,1,2,2,2,3,3,3,4,4,4,5,5,5,6,6,6,7,7,7,8,8,8,9,9,9};`

2. **Answer = 0**

3. For each `a` from 1 to 9  
   * `diff[i] = val[word[i]-'a'] - a`  (transformed value)  
   * Compute the prefix sum of `diff` and store each prefix in a hash‑map  
     (`prefix[sum]` = how many times this sum has appeared so far).  
   * Whenever the current prefix sum `s` is seen again, all sub‑arrays between the two positions have sum 0 → they all have average `a`.  
   * Increment the answer by the number of pairs of equal prefix sums.

4. Return the answer.

---

### 3. Pseudocode

```
ans = 0
for a in 1..9:
    map = empty hash map  // key = prefix sum, value = frequency
    map[0] = 1            // empty prefix
    cur = 0
    for each character ch in word:
        cur += value[ch] - a
        ans += map.getOrDefault(cur, 0)
        map[cur] = map.getOrDefault(cur, 0) + 1
return ans
```

---

### 4. Complexity analysis

| Metric | Expression | Result |
|--------|------------|--------|
| Time   | 9 · |word| | **O(9 · n)** (≈ 18 000 operations for the worst‑case `n = 2000`) |
| Memory | Hash map size ≈ |word| | **O(n)** for each pass, overall **O(n)** (the map is recreated 9 times, but each copy is discarded immediately) |

Both limits are easily met for the given constraints.

---

### 5. Reference implementations

> The following solutions follow the exact same logic and can be pasted directly into the LeetCode “Custom” tab.

---

#### 5.1 Java

```java
class Solution {
    // 1‑based mapping for 'a'..'z'
    private static final int[] MAP = {
        1, 1, 2, 2, 2, 3, 3, 3, 4, 4, 4,
        5, 5, 5, 6, 6, 6, 7, 7, 7, 8, 8, 8, 9, 9, 9
    };

    public long divisibleSubstrings(String word) {
        long ans = 0;
        int n = word.length();

        for (int avg = 1; avg <= 9; avg++) {
            // prefix sum hash map
            HashMap<Integer, Integer> prefCnt = new HashMap<>();
            prefCnt.put(0, 1);           // empty prefix

            int cur = 0;
            for (int i = 0; i < n; i++) {
                int val = MAP[word.charAt(i) - 'a'];
                cur += val - avg;          // transform to value - avg
                ans += prefCnt.getOrDefault(cur, 0);
                prefCnt.put(cur, prefCnt.getOrDefault(cur, 0) + 1);
            }
        }
        return ans;
    }
}
```

---

#### 5.2 Python

```python
class Solution:
    # mapping table: 0 → a, 1 → b, 2 → c, etc.
    _MAP = [
        1, 1, 2, 2, 2, 3, 3, 3, 4, 4, 4,
        5, 5, 5, 6, 6, 6, 7, 7, 7, 8, 8, 8, 9, 9, 9
    ]

    def divisibleSubstrings(self, word: str) -> int:
        ans = 0
        n = len(word)

        for avg in range(1, 10):
            pref = {0: 1}
            cur = 0
            for ch in word:
                cur += self._MAP[ord(ch) - 97] - avg
                ans += pref.get(cur, 0)
                pref[cur] = pref.get(cur, 0) + 1
        return ans
```

---

#### 5.3 C++

```cpp
class Solution {
public:
    long long divisibleSubstrings(string word) {
        static const int MAP[26] = {
            1, 1, 2, 2, 2, 3, 3, 3, 4, 4, 4,
            5, 5, 5, 6, 6, 6, 7, 7, 7, 8, 8, 8, 9, 9, 9
        };

        long long ans = 0;
        int n = word.size();

        for (int avg = 1; avg <= 9; ++avg) {
            unordered_map<int, int> prefCnt;
            prefCnt.reserve(n + 1);
            prefCnt[0] = 1;                 // empty prefix
            int cur = 0;
            for (char ch : word) {
                cur += MAP[ch - 'a'] - avg;
                ans += prefCnt[cur];
                ++prefCnt[cur];
            }
        }
        return ans;
    }
};
```

---

## 🚀 “How to Solve Leetcode 2950: Number of Divisible Substrings” – A Blog‑Style Guide

> **Target audience:** LeetCode enthusiasts, interviewers, data‑structure & algorithm students  
> **Core keywords:** *Leetcode 2950*, *Number of Divisible Substrings*, *divisible substrings*, *prefix sum trick*, *letter mapping to digits*

---

### 1. Why “Number of Divisible Substrings” sounds like a trick question

The name itself hints that we have to think about **divisibility** and **substrings**.  
If you ignore the letter‑to‑digit mapping and just brute‑force all `O(n²)` substrings, you’ll hit the time limit.  
The real trick is to recognize that the *average* of the mapped values has to be an **integer** (1–9).  

---

### 2. The “average = integer” trick

1. **Average = integer** – for a substring of length `p`

   ```
   (sum of mapped values) / p   must be an integer
   ```

2. **Fix an average `a`** (1–9) and ask  
   *“How many substrings have this exact average?”*

3. **Transform the array**  
   For each position `i` compute `value[i] - a`.  
   Now a substring has average `a` **iff** the transformed sum over that substring is **zero**.

4. **Count zero‑sum subarrays**  
   Classic prefix‑sum + hash‑map trick:  
   ```text
   pref[0] = 0
   for each i:
       pref[i+1] = pref[i] + diff[i]
   any pair (l,r) with pref[l] == pref[r] ⇒ sum of diff[l..r-1] == 0
   ```

5. **Repeat for all 9 averages** – total complexity `O(9 · n)`.

---

### 3. Why this beats the naive O(n²) solution

| Approach | Time | Reason |
|----------|------|--------|
| Brute‑force | O(n²) | 2000² ≈ 4 million checks – still okay, but the constant is high. |
| Prefix‑sum + average trick | O(9 · n) | 9 × 2000 = 18 000 operations – almost negligible. |
| Extra space | O(n) | Only one hash‑map per average. |

The LeetCode test‑suite is generous, but interviewers love solutions that run in linear time after a small constant factor.

---

### 4. Implementation details

#### 4.1 Mapping table

The problem defines a non‑alphabetic mapping:

```
a,b → 1
c,d,e → 2
f,g,h → 3
i,j,k → 4
l,m,n → 5
o,p,q → 6
r,s,t → 7
u,v,w → 8
x,y,z → 9
```

Most solutions pre‑create an array of length 26 so that `value[ch - 'a']` runs in O(1).

#### 4.2 Code snippets

Below are clean, production‑ready implementations in the three most common languages.

---

##### 4.2.1 Java

> Uses a `HashMap<Integer, Integer>` for the prefix frequencies.

```java
class Solution {
    private static final int[] MAP = { /* 26‑element mapping */ };

    public long divisibleSubstrings(String word) {
        long ans = 0;
        int n = word.length();

        for (int avg = 1; avg <= 9; avg++) {
            HashMap<Integer, Integer> prefCnt = new HashMap<>();
            prefCnt.put(0, 1);
            int cur = 0;
            for (int i = 0; i < n; i++) {
                cur += MAP[word.charAt(i) - 'a'] - avg;
                ans += prefCnt.getOrDefault(cur, 0);
                prefCnt.put(cur, prefCnt.getOrDefault(cur, 0) + 1);
            }
        }
        return ans;
    }
}
```

##### 4.2.2 Python

> Uses a dictionary for the prefix frequencies.

```python
class Solution:
    _MAP = [1,1,2,2,2,3,3,3,4,4,4,5,5,5,6,6,6,7,7,7,8,8,8,9,9,9]

    def divisibleSubstrings(self, word: str) -> int:
        ans = 0
        for avg in range(1, 10):
            pref = {0: 1}
            cur = 0
            for ch in word:
                cur += self._MAP[ord(ch)-97] - avg
                ans += pref.get(cur, 0)
                pref[cur] = pref.get(cur, 0) + 1
        return ans
```

##### 4.2.3 C++

> Uses `unordered_map` with `reserve` to avoid rehashing.

```cpp
class Solution {
public:
    long long divisibleSubstrings(string word) {
        static const int MAP[26] = { /* 26‑element mapping */ };
        long long ans = 0;
        int n = word.size();

        for (int avg = 1; avg <= 9; ++avg) {
            unordered_map<int,int> prefCnt;
            prefCnt.reserve(n+1);
            prefCnt[0] = 1;
            int cur = 0;
            for (char ch : word) {
                cur += MAP[ch-'a'] - avg;
                ans += prefCnt[cur];
                ++prefCnt[cur];
            }
        }
        return ans;
    }
};
```

---

### 5. Testing your solution

1. **Edge case**: `"aaaa"` – all values are 1, so every substring has average 1 → answer = `n*(n+1)/2`.  
2. **Random strings** – generate `word` of length 2000, check against the brute‑force O(n²) solver for correctness.  
3. **Stress test** – run many random cases to ensure no overflow (the answer fits in `long long`/`long`).

---

### 6. Takeaway

- Recognize hidden constraints: the average of the mapped digits must be an integer.
- Turn the problem into counting **zero‑sum subarrays** – a classic technique.
- Keep the mapping as an array for O(1) look‑ups.
- Repeat for each of the 9 possible averages – linear overall.

With this mindset, you’ll solve LeetCode 2950 in a flash and impress interviewers with a clean, efficient algorithm.

---

> *Happy coding, and may your next interview be a breeze!* 🚀

---



**End of the guide.**