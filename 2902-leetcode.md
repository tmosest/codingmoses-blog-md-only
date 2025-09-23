---
title: LeetCode 2902. Count of Sub-Multisets With Bounded Sum - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 2902. Count of Sub‑Multisets With Bounded Sum  
*LeetCode – Hard – Dynamic Programming – Knapsack*

| # | # | Language | Code |
|---|---|----------|------|
| 1 | 1 | **Java** | <details><summary>Click to expand</summary><pre><code class="java">import java.util.*;<br/><br/>class Solution {<br/>    private static final int MOD = 1_000_000_007;<br/><br/>    public int countSubMultisets(List&lt;Integer&gt; nums, int l, int r) {<br/>        // 1️⃣  Count the frequency of each value (multiset key).<br/>        TreeMap&lt;Integer, Integer&gt; freqMap = new TreeMap&lt;&gt;();<br/>        for (int x : nums) {<br/>            freqMap.merge(x, 1, Integer::sum);<br/>        }<br/><br/>        // 2️⃣  DP array – ways to reach each sum (0 … r).<br/>        long[] dp = new long[r + 1];<br/>        dp[0] = 1; // empty multiset<br/><br/>        for (Map.Entry&lt;Integer, Integer&gt; e : freqMap.entrySet()) {<br/>            int val = e.getKey();<br/>            int cnt = e.getValue();<br/><br/>            // 3️⃣  Special case – zero value (doesn't change the sum).<br/>            if (val == 0) {<br/>                long mul = (long) cnt + 1; // each zero can be taken 0..cnt times<br/>                for (int i = 0; i <= r; ++i) dp[i] = (dp[i] * mul) % MOD;<br/>                continue;<br/>            }<br/><br/>            // 4️⃣  Build prefix sums for the current value.<br/>            long[] pref = new long[r + 1];<br/>            pref[0] = dp[0];<br/>            for (int i = 1; i <= r; ++i) {<br/>                pref[i] = pref[i - 1];<br/>                if (i >= val) {<br/>                    pref[i] += pref[i - val];<br/>                    if (pref[i] >= MOD) pref[i] -= MOD;<br/>                }<br/>            }<br/><br/>            // 5️⃣  Update dp using the sliding window trick.<br/>            for (int i = r; i >= 0; --i) {<br/>                long total = pref[i];<br/>                int j = i - (cnt + 1) * val;<br/>                if (j >= 0) {<br/>                    total -= pref[j];<br/>                    if (total < 0) total += MOD;<br/>                }<br/>                dp[i] = total;<br/>            }<br/>        }<br/><br/>        // 6️⃣  Sum answers in the range [l, r].<br/>        long ans = 0;<br/>        for (int i = l; i <= r; ++i) {<br/>            ans += dp[i];<br/>            if (ans >= MOD) ans -= MOD;<br/>        }<br/>        return (int) ans;<br/>    }<br/>}</code></pre></details> |

| # | # | Language | Code |
|---|---|----------|------|
| 1 | 2 | **Python** | <details><summary>Click to expand</summary><pre><code class="python">from collections import Counter<br/><br/>MOD = 1_000_000_007<br/><br/>class Solution:<br/>    def countSubMultisets(self, nums: list[int], l: int, r: int) -> int:<br/>        freq = Counter(nums)<br/><br/>        dp = [0] * (r + 1)<br/>        dp[0] = 1  # empty multiset<br/><br/>        for val, cnt in sorted(freq.items()):<br/>            if val == 0:<br/>                mul = cnt + 1<br/>                for i in range(r + 1):<br/>                    dp[i] = (dp[i] * mul) % MOD<br/>                continue<br/><br/>            # prefix sums for current value<br/>            pref = [0] * (r + 1)<br/>            pref[0] = dp[0]<br/>            for i in range(1, r + 1):<br/>                pref[i] = pref[i - 1]<br/>                if i >= val:<br/>                    pref[i] += pref[i - val]<br/>                    if pref[i] >= MOD:<br/>                        pref[i] -= MOD<br/>            # update dp using sliding window\n            for i in range(r, -1, -1):<br/>                total = pref[i]<br/>                j = i - (cnt + 1) * val<br/>                if j >= 0:<br/>                    total -= pref[j]<br/>                    if total < 0:<br/>                        total += MOD<br/>                dp[i] = total<br/><br/>        return sum(dp[l:r+1]) % MOD</code></pre></details> |

| # | # | Language | Code |
|---|---|----------|------|
| 1 | 3 | **C++** | <details><summary>Click to expand</summary><pre><code class="cpp">#include &lt;bits/stdc++.h&gt;<br/><br/>using namespace std;<br/><br/>class Solution {<br/>public:<br/>    static const int MOD = 1'000'000'007;<br/><br/>    int countSubMultisets(vector&lt;int&gt; &amp;nums, int l, int r) {<br/>        unordered_map&lt;int, int&gt; cnt;<br/>        for (int x : nums) ++cnt[x];<br/><br/>        vector&lt;long long&gt; dp(r + 1, 0);<br/>        dp[0] = 1;<br/><br/>        for (auto &kv : cnt) {<br/>            int val = kv.first, c = kv.second;<br/>            if (val == 0) {<br/>                long long mul = c + 1LL;<br/>                for (int i = 0; i <= r; ++i) dp[i] = (dp[i] * mul) % MOD;<br/>                continue;<br/>            }<br/><br/>            vector&lt;long long&gt; pref(r + 1, 0);<br/>            pref[0] = dp[0];<br/>            for (int i = 1; i <= r; ++i) {<br/>                pref[i] = pref[i - 1];<br/>                if (i >= val) {<br/>                    pref[i] += pref[i - val];<br/>                    if (pref[i] >= MOD) pref[i] -= MOD;<br/>                }<br/>            }<br/><br/>            for (int i = r; i >= 0; --i) {<br/>                long long total = pref[i];<br/>                int j = i - (c + 1) * val;<br/>                if (j >= 0) {<br/>                    total -= pref[j];<br/>                    if (total &lt; 0) total += MOD;<br/>                }<br/>                dp[i] = total;<br/>            }<br/>        }<br/><br/>        long long ans = 0;<br/>        for (int i = l; i &lt;= r; ++i) {<br/>            ans += dp[i];<br/>            if (ans &gt;= MOD) ans -= MOD;<br/>        }<br/>        return (int)ans;<br/>    }<br/>};</code></pre></details> |

---

## Blog Article: “The Good, The Bad, and The Ugly of LeetCode 2902”

> **Meta Description (≈155 chars):**  
> Learn how to crack LeetCode 2902 – Count of Sub‑Multisets With Bounded Sum. Step‑by‑step DP guide, pitfalls, and interview‑ready explanations.

---

### 1. Problem Recap  
You’re given an array `nums` (non‑negative, length ≤ 20 000, total sum ≤ 20 000) and a range `[l, r]`.  
**Goal:** Count how many *sub‑multisets* (choose each element 0–`count(x)` times) have a sum inside that range.  
All answers must be returned modulo `1 000 000 007`.

> **Why it matters:**  
> This is a classic *knapsack‑like* DP problem that often appears in senior software‑engineer interviews because it tests **combination counting** and **state compression**.

---

### 2. Constraints That Reveal the Path Forward  

| Feature | Insight |
|---------|---------|
| `nums` values are **multiset‑like** (duplicates allowed) | We only need the *frequency* of each value, not their positions. |
| Sum of all numbers ≤ 20 000 | The DP dimension can be bounded by `r`, so the state space is at most 20 001. |
| `nums` contains **0** as a possible value | Zero doesn’t affect the sum, but it doubles the number of valid combinations. |

---

### 2. Why Brute Force Fails  
Trying all 2^n subsets is impossible (`n` = 20 000).  
Even generating all *distinct* sub‑multisets with a combinatorial loop still explodes.  
The key observation: **We only care about the *sum* of a multiset, not the exact composition**. That invites dynamic programming.

---

### 3. The Classic DP: 0/1 Knapsack Revisited  
If each element could be used at most once, the problem would be a classic 0/1 knapsack:

```text
dp[s] = number of ways to reach sum s
for each element x:
    for s from r down to x:
        dp[s] += dp[s-x]
```

But in our case each value can appear *up to* `cnt(value)` times.  
We can treat each value as a *bounded* resource.

---

### 4. Optimizing with Prefix Sums (Sliding Window)  

#### 4.1. Sliding Window Idea  
For a fixed value `v` that appears `c` times, the new `dp[s]` after considering `v` is:

```
dp_new[s] = Σ_{k=0..c} dp_old[s - k*v]      (if s-k*v ≥ 0)
```

A naive implementation would need `O(c)` work for every `s`, which is still heavy.

#### 4.2. Prefix‑Sum Trick  
Define `pref[s] = Σ_{t=0..s} dp_old[t]` **with jumps of size `v`**:

```
pref[s] = pref[s-1] + (s>=v ? pref[s-v] : 0)
```

Then the window sum above becomes:

```
dp_new[s] = pref[s] - pref[s-(c+1)*v]   (take care of negative index)
```

Because `pref[s]` already aggregates the required terms, we can compute each `dp_new[s]` in **O(1)** after building `pref` once.

#### 4.3. Why It’s Faster  
- Building `pref` is `O(r)` per distinct value.  
- Updating `dp` is also `O(r)` (one reverse loop).  
- Total complexity is `O(D * r)` where `D` is the number of distinct values (`≤ 20 001`).  
  With the constraints this is comfortably below 1 second in Java/Python/C++.

---

### 5. Edge Cases That Trip You Up

| Case | Why It Matters | How We Handle It |
|------|----------------|------------------|
| `val == 0` | Zero doesn’t change the sum but multiplies the number of valid combinations. | Multiply all `dp[s]` by `(cnt + 1)` modulo `MOD`. |
| `l == 0` | The empty multiset is always a valid candidate. | `dp[0]` starts as `1`. |
| Large `cnt` | The sliding window can overshoot `r`. | Use `int j = s - (cnt+1)*val` and guard against negative indices. |
| Modulo overflow | Prefix sums can exceed `MOD`. | Reduce immediately after every addition/subtraction. |

---

### 6. Code‑Walkthrough (Java)

```java
for (Map.Entry<Integer, Integer> e : freqMap.entrySet()) {
    int val = e.getKey();
    int cnt = e.getValue();

    if (val == 0) {          // Zero special case
        long mul = (long) cnt + 1;
        for (int i = 0; i <= r; ++i)
            dp[i] = (dp[i] * mul) % MOD;
        continue;
    }

    // Build prefix sums
    long[] pref = new long[r + 1];
    pref[0] = dp[0];
    for (int i = 1; i <= r; ++i) {
        pref[i] = pref[i-1];
        if (i >= val) {
            pref[i] += pref[i - val];
            if (pref[i] >= MOD) pref[i] -= MOD;
        }
    }

    // Sliding window update
    for (int i = r; i >= 0; --i) {
        long total = pref[i];
        int j = i - (cnt + 1) * val;
        if (j >= 0) {
            total -= pref[j];
            if (total < 0) total += MOD;
        }
        dp[i] = total;
    }
}
```

> **Why the `pref` array matters:**  
> `pref[i]` is essentially the *cumulative* number of ways to reach any sum that ends with `i`. Subtracting a earlier `pref` gives a bounded window of size `(cnt+1)*val`.

---

### 7. Complexity

| **Language** | **Time** | **Space** |
|--------------|----------|-----------|
| Java / Python / C++ | **O(D · r)** (≤ ≈ 4 × 10⁸ operations in the worst‑case, but each inner loop is lightweight) | **O(r)** (≈ 20 001 × 8 bytes ≈ 160 kB) |

Because `r` ≤ 20 000, the algorithm easily fits into the 2‑second LeetCode limit and most interview interviewers’ runtime expectations.

---

### 8. Common Pitfalls to Avoid

| Pitfall | What Happens | Fix |
|---------|--------------|-----|
| Forgetting the *sorted* order of distinct values | The prefix sum logic relies on adding jumps of a fixed `val`. Mixing orders can double‑count. | `sorted(freq.items())` (Java) / `sorted(freq)` (Python) |
| Mishandling modulo during subtraction | Negative numbers wrap incorrectly, giving wrong counts. | Add `MOD` if the result is negative. |
| Ignoring the zero element | Failing to multiply all `dp[s]` by `(cnt+1)` produces under‑count. | Explicit `if (val == 0)` block. |
| Using a 1‑based DP array but iterating from `r` down to `0` incorrectly | Index `-1` causes `ArrayIndexOutOfBounds`. | Always check `if (j >= 0)` before accessing `pref[j]`. |

---

### 9. Talking It Out in an Interview

1. **State the Observations**  
   *“The array is a multiset – duplicates are allowed, so we only care about the value and its count.”*

2. **Explain the Naïve DP**  
   *“We’d keep a DP array `dp[sum]`. For each distinct value `v` that appears `c` times, we would add `dp[sum - k*v]` for `k=0…c`. That’s a bounded knapsack.”*

3. **Point Out the Performance Bottleneck**  
   *“If we naïvely loop over `k`, the inner loop becomes `O(c·r)`. With up to 20 000 distinct values, that’s infeasible.”*

4. **Introduce the Prefix‑Sum Optimization**  
   *“By building a prefix sum where we step by `v`, we can collapse the inner sum into an O(1) update using a sliding window.”*

5. **Handle Edge Cases**  
   *“Zero values are special: they don’t change the sum, but each zero can be used 0…c times – effectively a multiplier.”*

6. **Finish with Complexity**  
   *“Overall O(D·r) time and O(r) space, which passes the constraints.”*

---

### 10. Takeaway

- **Good:** The problem is a neat bounded‑knapsack variant with a clear DP solution.  
- **Bad:** The naïve implementation is too slow; you must recognize the need for optimization.  
- **Ugly:** Ignoring the `0` case or mishandling the modulo will give you WA or TLE.  

With the code snippets above in your toolbox and the interview script ready, you’ll turn LeetCode 2902 from a “hard” challenge into a confidence‑boosting win. Happy coding!