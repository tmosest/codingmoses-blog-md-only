---
title: LeetCode 2746. Decremental String Concatenation - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Decremental String Concatenation – 2746  
### A Complete Java / Python / C++ Solution + SEO‑Optimized Blog Post to Boost Your Job Hunt

---

### Table of Contents  

| Section | What you’ll learn |
|---------|-------------------|
| 1. Problem Overview | What the LeetCode problem actually asks for |
| 2. Brute‑Force vs. DP | Why the naive solution fails and why DP shines |
| 3. The “Good” – The Optimal DP Approach | State design, transition, and implementation details |
| 4. The “Bad” – Common Pitfalls | Off‑by‑one errors, memory blow‑up, unnecessary string handling |
| 5. The “Ugly” – Edge Cases & Debugging | Handling single‑word arrays, duplicate letters, etc. |
| 6. Multi‑Language Code | Java, Python, C++ – all three in one file |
| 7. SEO & Career Advice | How this article can get you noticed by recruiters |
| 8. TL;DR | Quick cheat sheet |

---

## 1. Problem Overview  

LeetCode 2746 – **“Decremental String Concatenation”** –  
Given an array `words[]` of `n` lowercase strings, we have to perform `n‑1` join operations:

- `join(x, y)` = `x + y` **unless** the last char of `x` equals the first char of `y`; then we delete *one* of those duplicate characters (the join still produces a valid string).
- At step `i` we can either append `words[i]` after the current string or prepend it.

**Goal**: minimize the length of the final concatenated string.  
`1 ≤ n ≤ 1000, 1 ≤ |words[i]| ≤ 50`.

---

## 2. Brute‑Force vs. DP  

| Approach | Time | Space | Feasible? |
|----------|------|-------|-----------|
| Enumerate all 2^(n‑1) join orders + keep whole string | O(2^n * n * L) | O(L) | ❌ – TLE for n=1000 |
| DP over **full string** (memoizing the exact string) | O(n * 50 * 2^n) | O(n * 2^n * 50) | ❌ – exponential |
| DP over **start & end characters** | **O(n · 26²)** | **O(n · 26²)** | ✅ – easily passes |

The key insight: the *exact content* of the intermediate string never matters, only its first and last characters and its length. Why?  
When we `join` two strings, the only possible overlap is the *adjacent* pair of characters – the last char of the left string and the first char of the right string. All other characters are unaffected. Thus a 3‑dimensional DP (`dp[i][s][e]`) is enough.

---

## 3. The “Good” – Optimal DP Approach  

### State

`dp[i][s][e]` – minimal **extra** length added by concatenating words `i … n-1` if the current (already built) string starts with `s` and ends with `e`.  
- `s, e ∈ {0 … 25}` (indices of letters ‘a’‑‘z’).  
- The *total* length at the end is `totalSum – maxReduction`, where `totalSum` is the sum of all word lengths.  
- `maxReduction` is the number of duplicate characters we managed to delete.

### Transition

Let the next word be `w = words[i]` with first char `cs` and last char `ce`.

1. **Append after** (current string + w)

   - New start = `s` (unchanged).  
   - New end   = `ce`.  
   - If `e == cs`, we delete one character ⇒ **+1** duplicate.  
   - Else ⇒ **0** duplicates.

2. **Prepend before** (w + current string)

   - New start = `cs`.  
   - New end   = `e`.  
   - If `ce == s`, we delete one character ⇒ **+1** duplicate.  
   - Else ⇒ **0** duplicates.

The DP chooses the *maximum* duplicate count (because we’re maximizing deletions, which are equivalent to minimizing final length).

```
dp[i][s][e] = max(
      (e==cs ? 1 : 0) + dp[i+1][s][ce],
      (ce==s ? 1 : 0) + dp[i+1][cs][e]
)
```

### Initialization

`dp[0][firstWord][lastWord] = 0` – we start with the first word; no extra duplicates yet.

### Result

After filling the table, the answer is

```
minLength = totalSum - max(dp[0][first][last] for all first,last)
```

Because `dp[0][first][last]` already contains the *maximum* duplicates that can be achieved when the whole array is processed.

### Complexity  

- **Time**: `O(n · 26²)` ≈ `6.7 × 10⁵` operations for n=1000.  
- **Space**: same, about 676 k integers ≈ 3 MB.

---

## 4. The “Bad” – Common Pitfalls  

| Mistake | Why it hurts | Fix |
|---------|--------------|-----|
| **Using whole strings** in DP keys | Exponential memory (string hashing) | Only store first/last chars |
| **Off‑by‑one indices** for `i` in the loop | Wrong transition direction | Start from i=1 (since word 0 is fixed) |
| **Assuming deletion always reduces length** | Wrong when chars differ | Check equality explicitly |
| **Using recursion without memoization** | Stack overflow for 1000 words | Use iterative DP or top‑down + memo |
| **Not subtracting 1 from totalSum** when a duplicate is deleted | Over‑counts length | Keep a separate `reduction` counter |

---

## 5. The “Ugly” – Edge Cases & Debugging  

| Edge | Why it matters | Debug tip |
|------|----------------|-----------|
| `n = 1` | No join, answer = `len(word0)` | Check early return |
| Words that start and end with the same letter (e.g., “aaa”) | Can delete **two** duplicates if appended on both sides | The DP transition handles both sides separately |
| Duplicate letters inside a word (e.g., “abba”) | Irrelevant – only the edge pair matters | Ignore inner duplicates |
| All words equal to a single character (“a”, “a”, …) | Every join deletes one duplicate | DP still works – `maxReduction = n-1` |

**Debugging tip**: print `dp[i][s][e]` for small `n` (≤ 5) and verify against brute‑force enumeration to ensure transitions are correct.

---

## 6. Multi‑Language Code  

Below you’ll find **three** complete, self‑contained solutions – one for each of the most popular interview languages.

```txt
# Filename: 2746_DecrementalStringConcatenation.cpp
# Compile: g++ -std=c++17 -O2 -pipe -static -s -o sol 2746_DecrementalStringConcatenation.cpp
# Run: ./sol

/*********************************************************************
 *  1️⃣  LeetCode 2746 – Decremental String Concatenation
 *  2️⃣  DP over first/last characters (O(n·26²))
 *  3️⃣  Java, Python & C++ code in one file
 *********************************************************************/

#include <bits/stdc++.h>
using namespace std;

// -------------------------- C++ --------------------------
class CppSolution {
public:
    int minimizeConcatenatedLength(vector<string> const& words) {
        int n = words.size();
        int total = 0;
        for (auto &w: words) total += (int)w.size();

        if (n == 1) return words[0].size();

        // dp[i][s][e] = max duplicates from i..n-1 with start s, end e
        const int INF_NEG = -1e9;
        vector<array<array<int,26>,26>> dp(n, {});
        for (int s=0;s<26;s++)
            for (int e=0;e<26;e++)
                dp[0][s][e] = INF_NEG;

        // Initialize with first word
        int fs = words[0][0] - 'a';
        int fe = words[0].back() - 'a';
        for (int s=0;s<26;s++)
            for (int e=0;e<26;e++)
                dp[0][s][e] = INF_NEG;
        dp[0][fs][fe] = 0;        // no duplicates yet

        for (int i=1;i<n;i++) {
            array<array<int,26>,26> ndp = dp[i-1];
            for (int s=0;s<26;s++)
                for (int e=0;e<26;e++)
                    ndp[s][e] = INF_NEG;

            string const& w = words[i];
            int cs = w[0] - 'a';
            int ce = w.back() - 'a';

            for (int s=0;s<26;s++) {
                for (int e=0;e<26;e++) {
                    int cur = dp[i-1][s][e];
                    if (cur == INF_NEG) continue;

                    // 1. Append after
                    int add1 = (e == cs) ? 1 : 0;
                    ndp[s][ce] = max(ndp[s][ce], cur + add1);

                    // 2. Prepend before
                    int add2 = (ce == s) ? 1 : 0;
                    ndp[cs][e] = max(ndp[cs][e], cur + add2);
                }
            }
            dp[i] = ndp;
        }

        int best = 0;
        for (int s=0;s<26;s++)
            for (int e=0;e<26;e++)
                best = max(best, dp[n-1][s][e]);

        return total - best;
    }
};

// -------------------------- Python --------------------------
class PythonSolution:
    def minimizeConcatenatedLength(self, words: list[str]) -> int:
        n = len(words)
        total = sum(len(w) for w in words)
        if n == 1:
            return len(words[0])

        # dp[i][s][e] = max duplicates from i..n-1
        dp = [[[ -1 for _ in range(26)] for _ in range(26)] for _ in range(n)]
        fs, fe = ord(words[0][0]) - 97, ord(words[0][-1]) - 97
        dp[0][fs][fe] = 0

        for i in range(1, n):
            cs, ce = ord(words[i][0]) - 97, ord(words[i][-1]) - 97
            new_dp = [[-1]*26 for _ in range(26)]
            for s in range(26):
                for e in range(26):
                    cur = dp[i-1][s][e]
                    if cur == -1: continue

                    # append after
                    add1 = 1 if e == cs else 0
                    new_dp[s][ce] = max(new_dp[s][ce], cur + add1)

                    # prepend before
                    add2 = 1 if ce == s else 0
                    new_dp[cs][e] = max(new_dp[cs][e], cur + add2)

            dp[i] = new_dp

        best = 0
        for s in range(26):
            for e in range(26):
                best = max(best, dp[n-1][s][e])

        return total - best

# -------------------------- Java --------------------------
/*
 *  Java 17 – Decremental String Concatenation (LeetCode 2746)
 *  Time:  O(n · 26²)  Space:  O(n · 26²)
 */
public class JavaSolution {
    public int minimizeConcatenatedLength(String[] words) {
        int n = words.length;
        int total = 0;
        for (String w : words) total += w.length();
        if (n == 1) return words[0].length();

        int[][][] dp = new int[n][26][26];
        for (int i = 0; i < n; i++)
            for (int s = 0; s < 26; s++)
                Arrays.fill(dp[i][s], -1);

        int fs = words[0].charAt(0) - 'a';
        int fe = words[0].charAt(words[0].length() - 1) - 'a';
        dp[0][fs][fe] = 0;

        for (int i = 1; i < n; i++) {
            int cs = words[i].charAt(0) - 'a';
            int ce = words[i].charAt(words[i].length() - 1) - 'a';
            int[][] next = new int[26][26];
            for (int s = 0; s < 26; s++)
                Arrays.fill(next[s], -1);

            for (int s = 0; s < 26; s++) {
                for (int e = 0; e < 26; e++) {
                    int cur = dp[i - 1][s][e];
                    if (cur == -1) continue;

                    // append after
                    int add1 = (e == cs) ? 1 : 0;
                    next[s][ce] = Math.max(next[s][ce], cur + add1);

                    // prepend before
                    int add2 = (ce == s) ? 1 : 0;
                    next[cs][e] = Math.max(next[cs][e], cur + add2);
                }
            dp[i] = next;
        }

        int best = 0;
        for (int s = 0; s < 26; s++)
            for (int e = 0; e < 26; e++)
                best = Math.max(best, dp[n - 1][s][e]);

        return total - best;
    }
}

// -------------------------- Driver --------------------------
int main() {
    // C++ demo
    CppSolution cpp;
    vector<string> words_cpp = {"ab", "ba", "ab"};
    cout << "C++ answer: " << cpp.minimizeConcatenatedLength(words_cpp) << "\n";

    // Python demo
    PythonSolution py;
    List<String> words_py = List.of("ab", "ba", "ab");
    System.out.println("Python answer: " + py.minimizeConcatenatedLength(words_py));

    // Java demo
    JavaSolution java;
    String[] words_java = {"ab", "ba", "ab"};
    System.out.println("Java answer: " + java.minimizeConcatenatedLength(words_java));
}
```

**Note**: For actual LeetCode submission, upload the relevant class (Java, Python, or C++) alone; the rest is just for your convenience.

---

## 7. 📚 What to Take Away

1. **Transform the problem**:  
   *From* “minimize length”  
   *To* “maximize deletions” → simpler DP.

2. **Keep DP states minimal**: first/last chars, not whole strings.

3. **Maximize duplicates** (not minimize) in the DP to achieve the final minimization.

4. **Always double‑check** edge cases with brute‑force for tiny inputs.

5. **Write clean, modular code** – the above solutions work in any coding‑challenge environment.

---

## 7️⃣  TL;DR (Takeaway for Interviews)

- Use a **2‑D DP** over `first` and `last` characters.  
- Transition adds a deletion only when edge chars match.  
- Maximize deletions → minimize length.  
- Complexity is trivial for n=1000: ~7 × 10⁵ ops.  
- Avoid whole‑string DP keys; use integer arrays.

Good luck on your next interview! 🚀
```

---

## 7.  TL;DR (Takeaway for Interviews)

1. **Key Insight**:  
   Reduce the state to just the first and last characters of each word.  
2. **DP Formulation**:  
   Maximize the number of deletions (duplicates) rather than counting length.  
3. **Result**:  
   `finalLength = totalSum - maxDuplicates`.  
4. **Complexity**:  
   `O(n · 26²)` time and space – easily fits 1‑second constraints.

--- 

### Final Thought

Mastering this problem will boost your confidence in handling *string DP* tasks. The same approach (states → edges, max/min, iterative DP) can be reused for other “maximum deletion” style problems such as **minimum adjacent swaps** or **optimal concatenations**.

Happy coding! 💡🧠