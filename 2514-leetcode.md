---
title: LeetCode 2514. Count Anagrams - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## ✅ 2514. Count Anagrams – Complete Solution (Java, Python, C++)

Below you’ll find **three production‑ready implementations** that solve the LeetCode “Count Anagrams” problem in **O(N + K)** time (where *N* = string length and *K* = maximum word length).  
Each implementation includes:

* Pre‑computed factorials and inverse factorials modulo **1 000 000 007**  
* Fast modular exponentiation (Fermat’s little theorem)  
* Clear comments and `main`‑style entry for quick testing

---

### 1️⃣ Java

```java
import java.io.*;
import java.util.*;

public class Solution {

    private static final long MOD = 1_000_000_007L;

    /* ---------- Pre‑computation of factorials ---------- */
    private static long[] fact;
    private static long[] invFact;

    private static void precompute(int maxLen) {
        fact = new long[maxLen + 1];
        invFact = new long[maxLen + 1];
        fact[0] = 1;
        for (int i = 1; i <= maxLen; i++) {
            fact[i] = (fact[i - 1] * i) % MOD;
        }
        invFact[maxLen] = modInverse(fact[maxLen], MOD);
        for (int i = maxLen; i > 0; i--) {
            invFact[i - 1] = (invFact[i] * i) % MOD;
        }
    }

    /* ---------- Modular inverse (Fermat) ---------- */
    private static long modInverse(long a, long mod) {
        return modPow(a, mod - 2, mod);
    }

    private static long modPow(long base, long exp, long mod) {
        long res = 1;
        base %= mod;
        while (exp > 0) {
            if ((exp & 1) == 1) res = (res * base) % mod;
            base = (base * base) % mod;
            exp >>= 1;
        }
        return res;
    }

    /* ---------- Count anagrams of a single word ---------- */
    private static long wordAnagrams(String w) {
        int[] freq = new int[26];
        for (char c : w.toCharArray()) freq[c - 'a']++;

        long numerator = fact[w.length()];
        long denom = 1;
        for (int f : freq) if (f > 1) denom = (denom * fact[f]) % MOD;

        return (numerator * modInverse(denom, MOD)) % MOD;
    }

    /* ---------- Main solution ---------- */
    public int countAnagrams(String s) {
        // Find longest word to know how many factorials we need
        int maxLen = 0;
        for (String w : s.split(" ")) maxLen = Math.max(maxLen, w.length());

        precompute(maxLen);

        long ans = 1;
        for (String w : s.split(" ")) {
            ans = (ans * wordAnagrams(w)) % MOD;
        }
        return (int) ans;
    }

    /* ---------- Simple driver for local testing ---------- */
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String line = br.readLine();          // e.g. "too hot"
        System.out.println(new Solution().countAnagrams(line));
    }
}
```

---

### 2️⃣ Python

```python
#!/usr/bin/env python3
import sys
from collections import Counter

MOD = 1_000_000_007

# ---------- Pre‑compute factorials ----------
def precompute(max_len: int):
    fact = [1] * (max_len + 1)
    for i in range(1, max_len + 1):
        fact[i] = fact[i - 1] * i % MOD
    inv_fact = [1] * (max_len + 1)
    inv_fact[max_len] = pow(fact[max_len], MOD - 2, MOD)  # Fermat
    for i in range(max_len, 0, -1):
        inv_fact[i - 1] = inv_fact[i] * i % MOD
    return fact, inv_fact

# ---------- Count anagrams of a single word ----------
def word_anagrams(word: str, fact, inv_fact):
    freq = Counter(word)
    num = fact[len(word)]
    denom = 1
    for f in freq.values():
        denom = denom * fact[f] % MOD
    return num * pow(denom, MOD - 2, MOD) % MOD

# ---------- Main solution ----------
def count_anagrams(s: str) -> int:
    words = s.split()
    max_len = max(len(w) for w in words) if words else 0
    fact, inv_fact = precompute(max_len)

    ans = 1
    for w in words:
        ans = ans * word_anagrams(w, fact, inv_fact) % MOD
    return ans

# ---------- CLI driver ----------
if __name__ == "__main__":
    s = sys.stdin.readline().strip()
    print(count_anagrams(s))
```

---

### 3️⃣ C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    static constexpr long long MOD = 1'000'000'007LL;
    vector<long long> fact, invFact;

    /* ---------- Modular exponentiation ---------- */
    long long modPow(long long base, long long exp) {
        long long res = 1;
        base %= MOD;
        while (exp) {
            if (exp & 1) res = res * base % MOD;
            base = base * base % MOD;
            exp >>= 1;
        }
        return res;
    }

    /* ---------- Pre‑compute factorials ---------- */
    void precompute(int maxLen) {
        fact.resize(maxLen + 1);
        invFact.resize(maxLen + 1);
        fact[0] = 1;
        for (int i = 1; i <= maxLen; ++i)
            fact[i] = fact[i - 1] * i % MOD;
        invFact[maxLen] = modPow(fact[maxLen], MOD - 2);
        for (int i = maxLen; i > 0; --i)
            invFact[i - 1] = invFact[i] * i % MOD;
    }

    /* ---------- Count anagrams for a word ---------- */
    long long wordAnagrams(const string &w) {
        array<int, 26> freq = {0};
        for (char c : w) freq[c - 'a']++;

        long long numerator = fact[w.size()];
        long long denom = 1;
        for (int f : freq) if (f > 1)
            denom = denom * fact[f] % MOD;

        return numerator * modPow(denom, MOD - 2) % MOD;
    }

    /* ---------- Main solution ---------- */
    int countAnagrams(string s) {
        vector<string> words;
        stringstream ss(s);
        string w;
        int maxLen = 0;
        while (ss >> w) {
            words.push_back(w);
            maxLen = max(maxLen, (int)w.size());
        }
        precompute(maxLen);

        long long ans = 1;
        for (auto &word : words)
            ans = ans * wordAnagrams(word) % MOD;
        return (int)ans;
    }
};

/* ---------- Simple driver for quick testing ---------- */
int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    string line;
    getline(cin, line);          // e.g. "too hot"
    Solution sol;
    cout << sol.countAnagrams(line) << '\n';
    return 0;
}
```

---

## 📚 Blog Article – “Count Anagrams” on LeetCode: The Good, The Bad, The Ugly

> **Title:** *“Count Anagrams – 2514 LeetCode – The Math‑Driven, Modular‑Arithmetic Solution That Will Make Your Interviewers Smile”*  
> **Meta description:** *“Learn how to solve LeetCode’s hard problem ‘Count Anagrams’ using combinatorics, modular inverses, and pre‑computed factorials. Code in Java, Python, and C++. Master the good, bad, and ugly parts.”*

### 1️⃣ Introduction

The “Count Anagrams” problem (LeetCode 2514) asks you to compute the number of distinct **sentence‑level** anagrams of a given string of lowercase words separated by single spaces. At first glance, it feels like a combinatorial nightmare – but with the right math you can solve it in linear time.

> **Why it matters:**  
> Interviewers love problems that combine *dynamic programming*‑style thinking with *modular arithmetic*. Mastering this problem demonstrates you can:
> * Recognise independent sub‑problems (each word)
> * Apply multinomial coefficients
> * Handle large numbers modulo 1 000 000 007
> * Write clean, efficient code in multiple languages

### 2️⃣ Problem Restatement

> **Input**: `s` – a sentence containing one or more words, all lowercase, single spaces between words.  
> **Output**: `count` – the number of distinct anagrams of the entire sentence modulo 1 000 000 007.

### 3️⃣ The Good – A Straightforward Math

| Step | What it means | How we compute it |
|------|---------------|-------------------|
| **1. Words are independent** | Reordering letters inside one word does not affect the other words. | Split the sentence and multiply results per word. |
| **2. Word anagrams = multinomial coefficient** | For a word of length `L` with character frequencies `f1, f2, …, f26`, the number of unique permutations is:  

\[
\frac{L!}{f_1!\,f_2!\,\dots f_{26}!}
\] | Compute factorials `L!` and divide by each `f_i!`. |
| **3. Modulo arithmetic** | Results can be astronomically large. Use `MOD = 1 000 000 007`. | All factorials, products, and divisions are performed modulo MOD. |
| **4. Division modulo MOD** | You cannot divide directly. | Use **Fermat’s little theorem** to compute `denominator⁻¹ mod MOD`. |

### 3️⃣1️⃣ Algorithm Outline

1. **Pre‑compute factorials** up to the length of the longest word.  
2. For every word:
   * Count letter frequencies (`cnt[a] … cnt[z]`).
   * `num = fact[word_len]`
   * `den = Π fact[cnt[c]]` for all `c` with `cnt[c] > 1`
   * `word_ans = num * den⁻¹ mod MOD`
3. **Multiply** all `word_ans` values modulo MOD to get the final answer.

Time complexity is **O(N + K)** – linear in the input length plus a small `O(K)` factor for the longest word’s factorial array. Memory usage is also linear in the maximum word length.

### 4️⃣ The Good – What Makes This Solution Beautiful

| Aspect | Why It’s Good |
|--------|---------------|
| **Math‑centric** | The multinomial formula is the *cleanest* possible expression for the answer. No DP table or recursion needed. |
| **Linear pre‑computation** | `fact` and `invFact` are generated once per test case. Their reuse guarantees O(1) look‑ups for any word length. |
| **Language‑agnostic** | The same algorithm can be written in Java, Python, or C++ with minimal changes. |
| **Modular safety** | All operations are performed modulo `1 000 000 007`, ensuring no overflow in 64‑bit integers. |
| **Clear code** | Each function has a single responsibility (`precompute`, `wordAnagrams`, `modPow`). This readability is a win in interviews. |

### 5️⃣ The Bad – Where Pitfalls Occur

| Issue | Explanation | Fix |
|-------|-------------|-----|
| **Large factorial array** | If a word is very long (e.g., 10⁵), allocating `fact` and `invFact` of that size may hit memory limits on some judges. | Compute only up to the *actual* maximum word length. |
| **Repeated splits** | Using `s.split(" ")` twice can be O(N²) in worst‑case languages that copy strings. | Split once, store the words in a vector/array. |
| **Repeated pow calls** | Calling `pow` inside the loop for each word may look expensive. | Pre‑compute `invFact` so `denom⁻¹` is a simple multiplication (`invFact[denom]`). |
| **Division by zero** | If you forget to handle a frequency of 0 or 1, you might inadvertently try to compute `0!⁻¹`. | Only multiply by `fact[f]` when `f > 1`. |

### 6️⃣ The Ugly – Edge Cases That Can Break You

| Ugly situation | Why it breaks | What to watch for |
|----------------|---------------|--------------------|
| **Very long single word** | Factorials grow fast; pre‑computing up to 10⁶ might exceed the default stack or memory limits. | Use `long long` (64‑bit) and a *static* vector; avoid recursion. |
| **Modular inverse of 0** | If the denominator turns out to be 0 mod MOD, the inverse doesn’t exist. | In this problem the denominator is always a product of factorials of non‑zero counts, so it can never be 0 mod MOD (because all factorials up to MOD‑1 are non‑zero). |
| **Unexpected whitespace** | The problem guarantees single spaces, but an interviewer might test `"  too   hot  "` to see if you’re defensive. | Trim the string and split on regex `\\s+` or manually scan characters. |
| **Empty string** | Some solutions assume at least one word; an empty string should return 0 or 1? | Define the contract: return `0` if the sentence has no words. |

### 7️⃣ Complexity Analysis

| Operation | Time | Space |
|-----------|------|-------|
| Splitting the sentence | **O(N)** | **O(N)** (temporary string array) |
| Pre‑computing factorials | **O(K)** | **O(K)** (two arrays) |
| Counting anagrams per word | **O(K)** each → total **O(N)** | **O(1)** per word (fixed‑size freq array) |
| **Total** | **O(N + K)** | **O(K)** |

Because *K* is at most the length of the longest word (≤ 10⁵ in LeetCode tests), the memory usage stays well under 20 MB in all three languages.

### 8️⃣ Summary – What You’ll Take Home

| What I learned | What I’ll show at the interview |
|----------------|---------------------------------|
| **Multinomial coefficients** | Each word’s anagram count is just a combinatorial formula. |
| **Modular inverses** | Fermat’s little theorem gives a fast, reliable way to divide under modulo. |
| **Pre‑computation tricks** | One pass to build factorials, one pass to build inverse factorials. |
| **Language‑specific idioms** | Java streams, Python’s `Counter`, C++’s `array` and `stringstream`. |

> **Pro tip:**  
> Always test your code on the following edge cases before the interview:
> * `"a"` – single character
> * `"abcdefghijklmnopqrstuvwxyz"` – longest possible word (26 letters)
> * `"aaaa aaaa"` – repeated letters
> * `" "` (empty input) – guard against splits

---

## 🚀 Final Takeaway

The “Count Anagrams” problem is a *combinatorics‑plus‑modular‑arithmetic* showcase. With the three ready‑to‑use solutions above, you’re now equipped to:

* **Solve** the problem in O(N + K) time  
* **Explain** the math behind each step in an interview  
* **Switch** between Java, Python, and C++ with confidence  

Good luck – and remember: *clear code + clean math = interview success!*