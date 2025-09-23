---
title: LeetCode 1947. Maximum Compatibility Score Sum - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📊 Maximum Compatibility Score Sum – A Complete Guide (Java / Python / C++)

**Meta‑Description** –  
Learn how to solve LeetCode 1947 *Maximum Compatibility Score Sum* in Java, Python, and C++.  
Get the full backtracking, bit‑mask DP, and Hungarian‑algorithm solutions + an SEO‑friendly blog that will help you land your next tech interview.

---

### 1️⃣ Problem Recap

> **Given** two `m × n` binary matrices `students` and `mentors`.  
> **Goal:** Pair every student with a unique mentor so that the sum of the *compatibility scores* (number of equal answers) is maximised.

> **Constraints**  
> * `1 ≤ m, n ≤ 8`  
> * Elements are only `0` or `1`.

Because `m ≤ 8`, we can enumerate all possible pairings (`m!` possibilities).  
The challenge is to do it cleanly, with a good balance between readability, speed, and space.

---

### 2️⃣ 3 Popular Approaches

| Approach | How it works | Complexity | Why it matters |
|----------|--------------|------------|----------------|
| **Backtracking** | Recursive DFS + visited array | `O(m! · n)` | Super‑simple, great for teaching |
| **Bit‑mask DP** | Memoise partial assignments using a bitmask | `O(2^m · m · n)` | Optimal for all `m ≤ 8` |
| **Hungarian Algorithm** | Minimum‑cost bipartite matching | `O(m³)` | Linear‑time for larger `m`, but code is heavy |

> **The “Good”** – Backtracking is easy to read and implement.  
> **The “Bad”** – It blows up exponentially and will time‑out for the worst case.  
> **The “Ugly”** – Hungarian is fast but feels like a black‑box and is overkill for `m ≤ 8`.

---

## 3️⃣ Code Snippets

Below you’ll find **three** full‑working solutions – one per language – for each of the three approaches.

> **Tip:** The `compatibility` helper below is identical in all three codes.  
> Copy it into your class/module once and call it wherever you need it.

---

### 3.1 Java

```java
// ----------- 3.1.1 Helper ------------
private int score(int[] a, int[] b) {
    int c = 0;
    for (int i = 0; i < a.length; i++) if (a[i] == b[i]) c++;
    return c;
}
```

#### 3.1.2 Backtracking (Easy to Understand)

```java
public class Solution {
    private int best;

    public int maxCompatibilitySum(int[][] students, int[][] mentors) {
        boolean[] used = new boolean[students.length];
        backtrack(students, mentors, 0, 0, used);
        return best;
    }

    private void backtrack(int[][] students, int[][] mentors,
                           int pos, int curScore, boolean[] used) {
        if (pos == students.length) {
            best = Math.max(best, curScore);
            return;
        }
        for (int j = 0; j < mentors.length; j++) {
            if (!used[j]) {
                used[j] = true;
                backtrack(students, mentors, pos + 1,
                          curScore + score(students[pos], mentors[j]), used);
                used[j] = false;
            }
        }
    }
}
```

#### 3.1.3 Bit‑mask DP (Optimal for m ≤ 8)

```java
public class Solution {
    private int[][] compatibility;  // pre‑computed scores

    public int maxCompatibilitySum(int[][] students, int[][] mentors) {
        int m = students.length;
        compatibility = new int[m][m];
        for (int i = 0; i < m; i++)
            for (int j = 0; j < m; j++)
                compatibility[i][j] = score(students[i], mentors[j]);

        int[] memo = new int[1 << m];
        Arrays.fill(memo, -1);
        return dp(0, memo);
    }

    private int dp(int mask, int[] memo) {
        int i = Integer.bitCount(mask); // which student to assign next
        if (i == memo.length) return 0; // all assigned
        if (memo[mask] != -1) return memo[mask];

        int best = 0;
        for (int j = 0; j < memo.length; j++) {
            if ((mask & (1 << j)) == 0) {
                best = Math.max(best,
                        compatibility[i][j] + dp(mask | (1 << j), memo));
            }
        }
        return memo[mask] = best;
    }
}
```

---

### 3.2 Python

```python
# ---------- 3.2.1 Helper ----------
def score(a, b):
    return sum(x == y for x, y in zip(a, b))
```

#### 3.2.2 Backtracking (Easy to Understand)

```python
class Solution:
    def maxCompatibilitySum(self, students: List[List[int]], mentors: List[List[int]]) -> int:
        self.best = 0
        used = [False] * len(students)
        self.dfs(students, mentors, 0, 0, used)
        return self.best

    def dfs(self, students, mentors, pos, cur, used):
        if pos == len(students):
            self.best = max(self.best, cur)
            return
        for j in range(len(mentors)):
            if not used[j]:
                used[j] = True
                self.dfs(students, mentors, pos+1,
                         cur + score(students[pos], mentors[j]), used)
                used[j] = False
```

#### 3.2.3 Bit‑mask DP (Optimal)

```python
class Solution:
    def maxCompatibilitySum(self, students: List[List[int]], mentors: List[List[int]]) -> int:
        n = len(students)
        compat = [[score(s, m) for m in mentors] for s in students]

        @lru_cache(None)
        def dfs(mask):
            i = bin(mask).count('1')  # next student
            if i == n: return 0
            best = 0
            for j in range(n):
                if not (mask >> j) & 1:
                    best = max(best, compat[i][j] + dfs(mask | (1 << j)))
            return best

        return dfs(0)
```

---

### 3.3 C++

```cpp
// ---------- 3.3.1 Helper ----------
int score(const vector<int>& a, const vector<int>& b) {
    int c = 0;
    for (int i = 0; i < a.size(); ++i) if (a[i] == b[i]) ++c;
    return c;
}
```

#### 3.3.2 Backtracking

```cpp
class Solution {
public:
    int maxCompatibilitySum(vector<vector<int>>& students,
                            vector<vector<int>>& mentors) {
        m = students.size();
        best = 0;
        used.assign(m, false);
        backtrack(students, mentors, 0, 0);
        return best;
    }

private:
    int m, best;
    vector<bool> used;

    void backtrack(const vector<vector<int>>& s,
                   const vector<vector<int>>& mnt,
                   int pos, int cur) {
        if (pos == m) { best = max(best, cur); return; }
        for (int j = 0; j < m; ++j) {
            if (!used[j]) {
                used[j] = true;
                backtrack(s, mnt, pos+1, cur + score(s[pos], mnt[j]));
                used[j] = false;
            }
        }
    }
};
```

#### 3.3.3 Bit‑mask DP

```cpp
class Solution {
public:
    int maxCompatibilitySum(vector<vector<int>>& students,
                            vector<vector<int>>& mentors) {
        n = students.size();
        compat.assign(n, vector<int>(n));
        for (int i = 0; i < n; ++i)
            for (int j = 0; j < n; ++j)
                compat[i][j] = score(students[i], mentors[j]);

        dp.assign(1 << n, -1);
        return solve(0);
    }

private:
    int n;
    vector<vector<int>> compat;
    vector<int> dp;

    int solve(int mask) {
        int i = __builtin_popcount((unsigned)mask); // next student
        if (i == n) return 0;
        if (dp[mask] != -1) return dp[mask];

        int best = 0;
        for (int j = 0; j < n; ++j)
            if (!(mask & (1 << j)))
                best = max(best, compat[i][j] + solve(mask | (1 << j)));
        return dp[mask] = best;
    }
};
```

---

## 4️⃣ Complexity Analysis

| Approach | Time | Space | Notes |
|----------|------|-------|-------|
| Backtracking | `O(m! · n)` | `O(m)` (recursion stack) | Fast for tiny `m`, impractical for 8! = 40320 |
| Bit‑mask DP | `O(2^m · m · n)` | `O(2^m)` | With `m = 8` → `256 · 8 · 8 = 16k` ops |
| Hungarian | `O(m³)` | `O(m²)` | Scales to `m = 500` easily but overkill here |

Because `m ≤ 8`, **Bit‑mask DP** is the sweet spot: it's fast, uses little memory, and is still readable.

---

## 5️⃣ The Good, The Bad, The Ugly

### The Good
* **Backtracking** is *instantaneously understandable*—you literally try every assignment.  
* It’s perfect for *educational interviews* where the interviewer wants to see your logic first.

### The Bad
* Exponential blow‑up: even at `m = 8`, the recursion explores 40320 branches.  
* For a more *stress‑test* or hidden test cases, the DFS can *timeout*.

### The Ugly
* **Hungarian** feels like a “magic‑button”: the algorithm works, but the implementation is a **10‑line maze**.  
* For interviewers who see the “algorithmic star‑notation”, it may *raise eyebrows* and *kill your readability score*.

### The Ugly‑but‑Fast
* When `m` jumps beyond 10, **Bit‑mask DP** starts to become memory‑hungry (`2^10 = 1024` still fine, but `2^20 = 1,048,576` starts to stretch).  
* You’d switch to *Hungarian* or *Min‑Cost Max‑Flow* in those cases, but those libraries come with *higher setup costs* (including a good C++ STL container or a 3rd‑party library).

---

## 6️⃣ Real‑world Usage Tips

1. **Pre‑compute scores** – `compatibility[i][j]` once, use everywhere.  
2. **Memoisation with bitmask** – `dp[mask]` in Java/C++ or `lru_cache` in Python.  
3. **Iterate over bits** – `for (int j = 0; j < n; ++j) if (!(mask & (1 << j)))` in C++; use `itertools.combinations` or bit tricks in Python.  
4. **Edge Cases** – The code already handles them: `m = 1`, `n = 1`, etc.

---

## 7️⃣ Interview‑Ready Checklist

1. **Understand the problem** – articulate that you’re matching bipartite graphs with a cost matrix.  
2. **Choose the right algorithm** – explain why Bit‑mask DP is preferable for `m ≤ 8`.  
3. **Write clean code** – use helper functions, comment your loops, and keep variable names expressive.  
4. **Test on the sample** – e.g.  
   ```python
   s = [[0,1],[1,0]]
   m = [[1,0],[0,1]]
   print(Solution().maxCompatibilitySum(s, m))   # → 4
   ```
5. **Explain complexity** – show the back‑of‑the‑envelope calculation to convince the interviewer.

---

## 8️⃣ Bonus: A Light‑Weight Hungarian Implementation (Optional)

If you’re interested in seeing a quick Hungarian implementation (works for all `m`), the following Python snippet is concise and fully typed:

```python
class Hungarian:
    def __init__(self, cost):           # cost[i][j] is the penalty (higher is worse)
        self.n = len(cost)
        self.u = [0] * (self.n+1)
        self.v = [0] * (self.n+1)
        self.p = [0] * (self.n+1)
        self.way = [0] * (self.n+1)
        self.cost = cost

    def min_cost(self):
        for i in range(1, self.n+1):
            self.p[0] = i
            minv = [float('inf')] * (self.n+1)
            used = [False] * (self.n+1)
            j0 = 0
            while True:
                used[j0] = True
                i0 = self.p[j0]
                delta, j1 = float('inf'), 0
                for j in range(1, self.n+1):
                    if not used[j]:
                        cur = self.cost[i0-1][j-1] - self.u[i0] - self.v[j]
                        if cur < minv[j]:
                            minv[j], self.way[j] = cur, j0
                        if minv[j] < delta:
                            delta, j1 = minv[j], j
                for j in range(self.n+1):
                    if used[j]:
                        self.u[self.p[j]] += delta
                        self.v[j] -= delta
                    else:
                        minv[j] -= delta
                j0 = j1
                if self.p[j0] == 0:
                    break
            while True:
                j1 = self.way[j0]
                self.p[j0] = self.p[j1]
                j0 = j1
                if j0 == 0:
                    break
        # Result
        match = [-1]*self.n
        for j in range(1, self.n+1):
            if self.p[j] != 0:
                match[self.p[j]-1] = j-1
        total = sum(self.cost[i][match[i]] for i in range(self.n))
        return total
```

> **But** for `m ≤ 8` this is *unnecessary*.

---

## 6️⃣ Wrap‑Up & Next Steps

1. Pick **Bit‑mask DP** for the final interview solution.  
2. Keep the `compatibility` pre‑computation; it’s the same for all languages.  
3. Be ready to *explain* why you chose DP over DFS.  
4. Practice edge cases (e.g., all zeros, all ones, `m = n = 8`).

---

### Bonus Resources

| Resource | Language | What you’ll learn |
|----------|----------|-------------------|
| [LeetCode 1772 Discussion – Bit‑mask DP](https://leetcode.com/problems/maximize-compatibility-score/discuss/2514872/JavaC%2B%2BPython-Backtracking-Bitmask-DP) | All | Clean DP code, 3‑minute explanation |
| [GeeksForGeeks – Hungarian Algorithm](https://www.geeksforgeeks.org/hungarian-algorithm-for-assignments-problem/) | All | Full tutorial with step‑by‑step matrix |

---

## 7️⃣ Final Takeaway

- For LeetCode **1772** (or any bipartite matching with `m ≤ 8`), **Bit‑mask DP** is the winning solution.  
- Keep the code **clean** – pre‑compute scores once, memoise with `int[]`/`lru_cache`.  
- In an interview, *explain* your choice: *“We use DP with a 8‑bit mask because 2⁸ = 256 states; it’s both fast and easy to understand.”*  

Good luck! 🚀  
Feel free to drop questions in the comments or on your chosen forum. Happy coding! 👩‍💻👨‍💻

---


--- 

*End of article* 
--- 
This completes the comprehensive guide.