---
title: LeetCode 3327. Check if DFS Strings Are Palindromes - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📖 Blog Article – “The Good, The Bad, and the Ugly of **LeetCode 3327**”

### TL;DR  
LeetCode 3327 asks you to decide, for every node in a rooted tree, whether the string produced by a **post‑order DFS** is a palindrome.  
The “easy” solution that simply rebuilds the string for every node is **O(n²)** and will time‑out on the 10⁵ limit.  
The fastest accepted solution uses **post‑order traversal + double rolling‑hash** to test palindromes in **O(n)** time and **O(n)** memory – the “good” part.  
The “bad” part is the subtlety of handling the reverse order correctly.  
The “ugly” part? A couple of “magic numbers” (two prime moduli) and the necessity to pre‑compute powers – but those are common tricks that you’ll learn to love.

> **SEO keywords**: *Leetcode 3327, Check if DFS Strings Are Palindromes, Tree Palindrome DFS, Java solution, Python solution, C++ solution, Rolling hash, Palindrome detection, Interview prep, Algorithmic interview, Tree traversal, Post‑order DFS, Complexity analysis*

---

### 1. Problem Overview

```
Input:
    parent[0…n-1]  (parent[0] = -1, all other parent[i] ∈ [0,n-1])
    s[0…n-1]       (lowercase letters)

Definition:
    dfs(x):
        for each child y of x in increasing order
            dfs(y)
        append s[x] to a global string dfsStr

Task:
    For every node i, start with an empty dfsStr, call dfs(i), and set
    answer[i] = true iff the resulting dfsStr is a palindrome.

Constraints: 1 ≤ n ≤ 10⁵
```

### 2. Intuition

The string produced by `dfs(v)` is exactly:

```
S(v) = S(child1) + S(child2) + … + S(childk) + s[v]
```

where the children are traversed in **increasing order**.  
A palindrome test for `S(v)` would normally require us to build the entire string for every node – **O(n²)** – which is impossible for n = 10⁵.

The key observation:  
**We can compute a forward hash and a reverse hash of each `S(v)` in one bottom‑up pass.**  
If the two hashes match (for two different moduli), `S(v)` is a palindrome with astronomically low probability of collision.

#### Forward hash
```
H_f(v) = (( ( H_f(child1) * P^{len(child1)} + H_f(child2) ) * P^{len(child2)} + … ) * P + code(s[v])
```

#### Reverse hash
The reverse of `S(v)` is
```
reverse(S(v)) = s[v] + reverse(S(childk)) + … + reverse(S(child1))
```
So we must build the hash in the **reverse child order**:

```
H_r(v) = (( code(s[v]) * P^{len(childk)} + H_r(childk) ) * P^{len(childk-1)} + H_r(childk-1) ) … 
```

Both hashes also require the exact length of the string at each node (`len[v]`) and powers of the base `P`.

---

### 3. Naïve Approach (Why It Fails)

```python
def dfs(v):
    for child in children[v]:
        dfs(child)
    res.append(s[v])

def solve():
    ans = [False]*n
    for v in range(n):
        res.clear()
        dfs(v)
        ans[v] = res == res[::-1]      # rebuild & reverse
```

*Time*: `O(∑|S(v)|) = O(n²)`  
*Memory*: O(n) per call – stack recursion will blow up in Java or C++.

**Result**: TLE on medium & hard tests.  
So we must do something smarter.

---

### 4. Optimized Approach – O(n) with Double Rolling Hash

| Step | What to compute | Why |
|------|-----------------|-----|
| Build adjacency list | children lists | Needed for traversal |
| Post‑order DFS (bottom‑up) | `len[v]`, `H_f[v]`, `H_r[v]` | Compute hashes from leaves to root |
| Compare hashes (mod1 & mod2) | `answer[v]` | Palindrome test |

#### Pre‑computation

```
P1 = 911382323,   MOD1 = 1_000_000_007
P2 = 972663749,   MOD2 = 1_000_000_009
pow1[i] = P1^i  (mod MOD1)
pow2[i] = P2^i  (mod MOD2)
```

We pre‑compute powers up to n so that combining a child’s hash only needs a single multiplication and addition.

#### Bottom‑up formula (forward)

```
val = 0
for child in children[v]          # increasing order
    val = (val * pow1[len[child]] + H_f[child]) % MOD1
    val = (val * pow2[len[child]] + H_f[child]) % MOD2

val = (val * P1 + code(s[v])) % MOD1
val = (val * P2 + code(s[v])) % MOD2

H_f[v] = val
```

#### Bottom‑up formula (reverse)

```
val = 0
val = (code(s[v]) * 1) % MOD1
val = (code(s[v]) * 1) % MOD2

for child in reversed(children[v]):  # reverse order
    val = (val * pow1[len[child]] + H_r[child]) % MOD1
    val = (val * pow2[len[child]] + H_r[child]) % MOD2

H_r[v] = val
```

*Note*: `code(ch)` is simply `ord(ch) - ord('a') + 1`.

#### Palindrome check

```
answer[v] = (H_f[v] == H_r[v])  # both modulo pairs must match
```

---

### 5. Implementation Details

Below are **ready‑to‑paste** solutions in **Java, Python, and C++**.  
All use the same algorithmic skeleton: build the tree, pre‑compute powers, perform an *iterative* post‑order DFS, then compare the two hashes.

> **Important** – we use *two* moduli to keep collision probability negligible.  
> The code is heavily commented so you can understand each step without the need to “reverse engineer” later.

---

#### 5.1 Java (Java 17)

```java
import java.io.*;
import java.util.*;

public class Solution {
    private static final long MOD1 = 1_000_000_007L;
    private static final long MOD2 = 1_000_000_009L;
    private static final long BASE1 = 911382323L;   // any random prime < MOD1
    private static final long BASE2 = 972663749L;   // any random prime < MOD2

    public static void main(String[] args) throws IOException {
        FastScanner fs = new FastScanner(System.in);
        int n = fs.nextInt();
        int[] parent = new int[n];
        for (int i = 0; i < n; i++) parent[i] = fs.nextInt();
        char[] s = fs.next().toCharArray();

        // 1. Build children lists
        List<Integer>[] g = new ArrayList[n];
        for (int i = 0; i < n; i++) g[i] = new ArrayList<>();
        for (int i = 1; i < n; i++) g[parent[i]].add(i);
        for (int i = 0; i < n; i++) Collections.sort(g[i]);

        // 2. Pre‑compute powers
        long[] pow1 = new long[n + 1];
        long[] pow2 = new long[n + 1];
        pow1[0] = pow2[0] = 1;
        for (int i = 1; i <= n; i++) {
            pow1[i] = (pow1[i - 1] * BASE1) % MOD1;
            pow2[i] = (pow2[i - 1] * BASE2) % MOD2;
        }

        // 3. Bottom‑up iterative post‑order
        int[] len = new int[n];
        long[] hf1 = new long[n], hf2 = new long[n];
        long[] hr1 = new long[n], hr2 = new long[n];
        boolean[] visited = new boolean[n];
        Deque<Integer> stack = new ArrayDeque<>();
        stack.push(0);

        while (!stack.isEmpty()) {
            int v = stack.peek();
            if (!visited[v]) {
                visited[v] = true;
                for (int child : g[v]) stack.push(child);
            } else {
                stack.pop();
                // compute length
                int total = 1;
                for (int child : g[v]) total += len[child];
                len[v] = total;

                // forward hash
                long f1 = 0, f2 = 0;
                for (int child : g[v]) {
                    f1 = (f1 * pow1[len[child]] + hf1[child]) % MOD1;
                    f2 = (f2 * pow2[len[child]] + hf2[child]) % MOD2;
                }
                f1 = (f1 * BASE1 + (s[v] - 'a' + 1)) % MOD1;
                f2 = (f2 * BASE2 + (s[v] - 'a' + 1)) % MOD2;
                hf1[v] = f1; hf2[v] = f2;

                // reverse hash
                long r1 = (s[v] - 'a' + 1) % MOD1;
                long r2 = (s[v] - 'a' + 1) % MOD2;
                for (int i = g[v].size() - 1; i >= 0; i--) {
                    int child = g[v].get(i);
                    r1 = (r1 * pow1[len[child]] + hr1[child]) % MOD1;
                    r2 = (r2 * pow2[len[child]] + hr2[child]) % MOD2;
                }
                hr1[v] = r1; hr2[v] = r2;
            }
        }

        // 4. Build answer
        StringBuilder out = new StringBuilder();
        for (int i = 0; i < n; i++) {
            boolean ok = (hf1[i] == hr1[i]) && (hf2[i] == hr2[i]);
            out.append(ok ? "true" : "false");
            if (i + 1 < n) out.append(' ');
        }
        System.out.println(out);
    }

    /* ---------- fast scanner ---------- */
    private static class FastScanner {
        private final InputStream in;
        private final byte[] buffer = new byte[1 << 16];
        private int ptr = 0, len = 0;
        FastScanner(InputStream in) { this.in = in; }
        private int readByte() throws IOException {
            if (ptr >= len) {
                len = in.read(buffer);
                ptr = 0;
                if (len <= 0) return -1;
            }
            return buffer[ptr++];
        }
        int nextInt() throws IOException {
            int c, sign = 1, val = 0;
            while ((c = readByte()) <= ' ') if (c == -1) return -1;
            if (c == '-') { sign = -1; c = readByte(); }
            while (c > ' ') { val = val * 10 + c - '0'; c = readByte(); }
            return val * sign;
        }
        String next() throws IOException {
            StringBuilder sb = new StringBuilder();
            int c;
            while ((c = readByte()) <= ' ') if (c == -1) return null;
            while (c > ' ') { sb.append((char) c); c = readByte(); }
            return sb.toString();
        }
    }
}
```

---

#### 5.2 Python

```python
import sys
from collections import defaultdict, deque

MOD1 = 1_000_000_007
MOD2 = 1_000_000_009
BASE1 = 911382323
BASE2 = 972663749

def solve():
    data = sys.stdin.read().strip().split()
    it = iter(data)
    n = int(next(it))
    parent = [int(next(it)) for _ in range(n)]
    s = list(next(it))

    # Build tree
    g = [[] for _ in range(n)]
    for i in range(1, n):
        g[parent[i]].append(i)
    for lst in g:
        lst.sort()

    # Pre‑compute powers
    pow1 = [1]*(n+1)
    pow2 = [1]*(n+1)
    for i in range(1, n+1):
        pow1[i] = (pow1[i-1] * BASE1) % MOD1
        pow2[i] = (pow2[i-1] * BASE2) % MOD2

    # Bottom‑up DFS
    lenv = [0]*n
    hf1 = [0]*n; hf2 = [0]*n
    hr1 = [0]*n; hr2 = [0]*n
    visited = [False]*n
    stack = [0]
    while stack:
        v = stack[-1]
        if not visited[v]:
            visited[v] = True
            for c in g[v]: stack.append(c)
        else:
            stack.pop()
            total = 1
            for c in g[v]: total += lenv[c]
            lenv[v] = total

            # forward hash
            f1, f2 = 0, 0
            for c in g[v]:
                f1 = (f1 * pow1[lenv[c]] + hf1[c]) % MOD1
                f2 = (f2 * pow2[lenv[c]] + hf2[c]) % MOD2
            f1 = (f1 * BASE1 + (ord(s[v]) - 96)) % MOD1
            f2 = (f2 * BASE2 + (ord(s[v]) - 96)) % MOD2
            hf1[v], hf2[v] = f1, f2

            # reverse hash
            r1, r2 = (ord(s[v]) - 96) % MOD1, (ord(s[v]) - 96) % MOD2
            for c in reversed(g[v]):
                r1 = (r1 * pow1[lenv[c]] + hr1[c]) % MOD1
                r2 = (r2 * pow2[lenv[c]] + hr2[c]) % MOD2
            hr1[v], hr2[v] = r1, r2

    ans = [ (hf1[i]==hr1[i] and hf2[i]==hr2[i]) for i in range(n) ]
    print(' '.join('true' if x else 'false' for x in ans))

if __name__ == "__main__":
    solve()
```

---

#### 5.3 C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

const long long MOD1 = 1'000'000'007LL;
const long long MOD2 = 1'000'000'009LL;
const long long BASE1 = 911382323LL;   // random prime < MOD1
const long long BASE2 = 972663749LL;   // random prime < MOD2

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    int n; 
    if (!(cin >> n)) return 0;
    vector<int> parent(n);
    for (int i = 0; i < n; ++i) cin >> parent[i];
    string str; cin >> str;
    vector<char> s(str.begin(), str.end());

    // Build children list
    vector<vector<int>> g(n);
    for (int i = 1; i < n; ++i) g[parent[i]].push_back(i);
    for (int i = 0; i < n; ++i) sort(g[i].begin(), g[i].end());

    // Pre‑compute powers
    vector<long long> pow1(n + 1, 1), pow2(n + 1, 1);
    for (int i = 1; i <= n; ++i) {
        pow1[i] = (pow1[i-1] * BASE1) % MOD1;
        pow2[i] = (pow2[i-1] * BASE2) % MOD2;
    }

    // Bottom‑up iterative DFS
    vector<int> len(n, 0);
    vector<long long> hf1(n), hf2(n), hr1(n), hr2(n);
    vector<bool> vis(n, false);
    vector<int> stack = {0};

    while (!stack.empty()) {
        int v = stack.back();
        if (!vis[v]) {
            vis[v] = true;
            for (int child : g[v]) stack.push_back(child);
        } else {
            stack.pop_back();
            int total = 1;
            for (int child : g[v]) total += len[child];
            len[v] = total;

            // forward hash
            long long f1 = 0, f2 = 0;
            for (int child : g[v]) {
                f1 = (f1 * pow1[len[child]] + hf1[child]) % MOD1;
                f2 = (f2 * pow2[len[child]] + hf2[child]) % MOD2;
            }
            f1 = (f1 * BASE1 + (s[v] - 'a' + 1)) % MOD1;
            f2 = (f2 * BASE2 + (s[v] - 'a' + 1)) % MOD2;
            hf1[v] = f1; hf2[v] = f2;

            // reverse hash
            long long r1 = (s[v] - 'a' + 1) % MOD1;
            long long r2 = (s[v] - 'a' + 1) % MOD2;
            for (int i = (int)g[v].size() - 1; i >= 0; --i) {
                int child = g[v][i];
                r1 = (r1 * pow1[len[child]] + hr1[child]) % MOD1;
                r2 = (r2 * pow2[len[child]] + hr2[child]) % MOD2;
            }
            hr1[v] = r1; hr2[v] = r2;
        }
    }

    // Output
    for (int i = 0; i < n; ++i) {
        bool ok = (hf1[i] == hr1[i]) && (hf2[i] == hr2[i]);
        cout << (ok ? "true" : "false") << (i + 1 < n ? ' ' : '\n');
    }
}
```

---

### 6. Complexity Analysis

| Operation | Time Complexity | Memory Complexity |
|-----------|-----------------|--------------------|
| Building tree | `O(n)` | `O(n)` |
| Power pre‑calc | `O(n)` | `O(n)` |
| Iterative post‑order DFS | `O(n)` | `O(n)` (arrays) |
| Final answer | `O(n)` | `O(1)` |

Overall: **`O(n)` time, `O(n)` memory** – well within limits for 10⁵ nodes.

---

### 7. Summary

- **Problem**: Determine if each node’s DFS string is a palindrome.  
- **Naïve**: O(n²) – fails.  
- **Optimized**: Use *double rolling hash* with *bottom‑up* traversal, computing forward and reverse hashes in correct child orders.  
- **Complexity**: `O(n)` time, `O(n)` memory.  
- **All languages**: The same logic, with clear comments and fast I/O.

With this solution you can confidently tackle any medium/hard test in LeetCode **Postorder DFS Palindrome** or similar tree hashing problems. Good luck, and happy coding!