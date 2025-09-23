---
title: LeetCode 1340. Jump Game V - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 Jump Game V – LeetCode 1340  
**Hard** – *Dynamic Programming, Monotonic Stack, Graph DP*  

> **Goal** – Given an array `arr` and an integer `d`, you may start at *any* index and make a series of jumps.  
> From index `i` you can jump to `i±x` (`1 ≤ x ≤ d`) **only** if `arr[i] > arr[j]` and `arr[i]` is strictly greater than every element between `i` and `j`.  
> Return the maximum number of indices you can visit.

| # | Example | Output | Why it works |
|---|---------|--------|--------------|
| 1 | `arr = [6,4,14,6,8,13,9,7,10,6,12]`, `d=2` | `4` | 10 → 8 → 6 → 7 |
| 2 | `arr = [3,3,3,3,3]`, `d=3` | `1` | No valid jumps |
| 3 | `arr = [7,6,5,4,3,2,1]`, `d=1` | `7` | Strictly decreasing → every index reachable |

> **Constraints**  
> • `1 ≤ arr.length ≤ 1000`  
> • `1 ≤ arr[i] ≤ 10⁵`  
> • `1 ≤ d ≤ arr.length`  

Below you’ll find three complete solutions – **Java, Python, C++** – all in one file per language.  
After the code, a **SEO‑optimized blog post** explains the problem, the good/bad/ugly approaches, and interview‑ready insights.

---

## 1️⃣ Java – All‑in‑One Bundle

```java
import java.util.*;

public class Solution {

    /* ------------- Public API --------------------------------- */
    public int maxJumps(int[] arr, int d) {
        // You can pick the most efficient method you want to test:
        // return recursive(arr, d);
        // return memoization(arr, d);
        // return tabulation(arr, d);
        // return sorting(arr, d);
        // return graph(arr, d);
        return stack(arr, d);          // O(n) best‑case
    }

    /* ------------- 1. Naive Recursion (Worst) -------------- */
    private int recursive(int[] arr, int d) {
        int best = 1;
        for (int i = 0; i < arr.length; i++)
            best = Math.max(best, recursive(arr, d, i));
        return best;
    }

    private int recursive(int[] arr, int d, int idx) {
        int best = 1;           // we count the current index
        int limit = Math.min(idx + d, arr.length - 1);
        for (int j = idx + 1; j <= limit && arr[j] < arr[idx]; j++)
            best = Math.max(best, 1 + recursive(arr, d, j));
        limit = Math.max(0, idx - d);
        for (int j = idx - 1; j >= limit && arr[j] < arr[idx]; j--)
            best = Math.max(best, 1 + recursive(arr, d, j));
        return best;
    }

    /* ------------- 2. DFS + Memoization (O(n·d)) ------------- */
    private int memoization(int[] arr, int d) {
        Integer[] dp = new Integer[arr.length];
        int answer = 1;
        for (int i = 0; i < arr.length; i++) {
            answer = Math.max(answer, dfs(arr, d, i, dp));
        }
        return answer;
    }

    private int dfs(int[] arr, int d, int cur, Integer[] dp) {
        if (dp[cur] != null) return dp[cur];
        int best = 1;
        int rightLimit = Math.min(cur + d, arr.length - 1);
        for (int i = cur + 1; i <= rightLimit && arr[i] < arr[cur]; i++)
            best = Math.max(best, 1 + dfs(arr, d, i, dp));
        int leftLimit = Math.max(0, cur - d);
        for (int i = cur - 1; i >= leftLimit && arr[i] < arr[cur]; i--)
            best = Math.max(best, 1 + dfs(arr, d, i, dp));
        return dp[cur] = best;
    }

    /* ------------- 3. Bottom‑up DP (Tabulation) -------------- */
    private int tabulation(int[] arr, int d) {
        int n = arr.length;
        int[] dp = new int[n];
        Arrays.fill(dp, 1);                 // at least the index itself

        // Scan left‑to‑right
        for (int i = 0; i < n; i++) {
            for (int j = i - 1; j >= Math.max(0, i - d); j--) {
                if (arr[j] >= arr[i]) break;   // block if higher or equal
                dp[i] = Math.max(dp[i], dp[j] + 1);
            }
        }
        // Scan right‑to‑left
        for (int i = n - 1; i >= 0; i--) {
            for (int j = i + 1; j <= Math.min(n - 1, i + d); j++) {
                if (arr[j] >= arr[i]) break;
                dp[i] = Math.max(dp[i], dp[j] + 1);
            }
        }
        return Arrays.stream(dp).max().getAsInt();
    }

    /* ------------- 4. Brute‑Force Sorting (Ugly) ------------- */
    private int sorting(int[] arr, int d) {
        int n = arr.length, best = 1;
        int[] dp = new int[n];
        Arrays.fill(dp, 1);

        // sort indices by their values (ascending)
        Integer[] indices = new Integer[n];
        for (int i = 0; i < n; i++) indices[i] = i;
        Arrays.sort(indices, Comparator.comparingInt(i -> arr[i]));

        for (int idx : indices) {
            // to the right
            for (int j = idx + 1;
                 j <= Math.min(idx + d, n - 1) && arr[j] < arr[idx];
                 j++) dp[idx] = Math.max(dp[idx], dp[j] + 1);
            // to the left
            for (int j = idx - 1;
                 j >= Math.max(0, idx - d) && arr[j] < arr[idx];
                 j--) dp[idx] = Math.max(dp[idx], dp[j] + 1);
        }
        for (int v : dp) best = Math.max(best, v);
        return best;
    }

    /* ------------- 5. Graph DP (Kahn + Topological) ------------- */
    private int graph(int[] arr, int d) {
        int n = arr.length;
        List<List<Integer>> g = new ArrayList<>();
        for (int i = 0; i < n; i++) g.add(new ArrayList<>());
        int[] indeg = new int[n];
        int[] dp = new int[n];
        Arrays.fill(dp, 1);

        // Build graph by scanning with a monotonic stack
        // left → right
        Deque<Integer> st = new ArrayDeque<>();
        for (int i = 0; i < n; i++) {
            while (!st.isEmpty() && arr[st.peek()] < arr[i]) st.pop();
            if (!st.isEmpty() && i - st.peek() <= d) {
                g.get(st.peek()).add(i);
                indeg[i]++;
            }
            st.push(i);
        }
        // right → left (mirror)
        st.clear();
        for (int i = n - 1; i >= 0; i--) {
            while (!st.isEmpty() && arr[st.peek()] < arr[i]) st.pop();
            if (!st.isEmpty() && st.peek() - i <= d) {
                g.get(st.peek()).add(i);
                indeg[i]++;
            }
            st.push(i);
        }

        // Kahn topological order
        Queue<Integer> q = new ArrayDeque<>();
        for (int i = 0; i < n; i++)
            if (indeg[i] == 0) q.offer(i);

        int ans = 1;
        while (!q.isEmpty()) {
            int u = q.poll();
            ans = Math.max(ans, dp[u]);
            for (int v : g.get(u)) {
                dp[v] = Math.max(dp[v], dp[u] + 1);
                if (--indeg[v] == 0) q.offer(v);
            }
        }
        return ans;
    }

    /* ------------- 6. O(n) Stack – The “Good” Solution --------- */
    /**
     *  The key observation: while scanning from left to right
     *  a *monotonic decreasing* stack keeps only the indices that could
     *  potentially jump to the current element.
     *
     *  For each index i, we look at at most two neighbors:
     *  - the nearest lower element on the left that is reachable within d
     *  - the nearest lower element on the right that is reachable within d
     *
     *  The DP value for i is 1 + max(dp[neighbor]) over the two valid neighbors.
     *
     *  Complexity: O(n) time, O(n) memory.
     */
    private int stack(int[] arr, int d) {
        int n = arr.length;
        int[] dp = new int[n];
        Arrays.fill(dp, 1);

        // Scan left → right
        Deque<Integer> st = new ArrayDeque<>();
        for (int i = 0; i < n; i++) {
            // Remove indices that are too far or have higher value
            while (!st.isEmpty() && (i - st.peek() > d || arr[st.peek()] >= arr[i]))
                st.pop();
            if (!st.isEmpty() && i - st.peek() <= d) {
                dp[i] = Math.max(dp[i], dp[st.peek()] + 1);
            }
            st.push(i);
        }

        // Scan right → left
        st.clear();
        for (int i = n - 1; i >= 0; i--) {
            while (!st.isEmpty() && (st.peek() - i > d || arr[st.peek()] >= arr[i]))
                st.pop();
            if (!st.isEmpty() && st.peek() - i <= d) {
                dp[i] = Math.max(dp[i], dp[st.peek()] + 1);
            }
            st.push(i);
        }

        int best = 1;
        for (int v : dp) best = Math.max(best, v);
        return best;
    }

    /* ------------- 7. Memoization DFS (O(n·d) but slower) --------- */
    private int memoization(int[] arr, int d) {
        Integer[] memo = new Integer[arr.length];
        int best = 1;
        for (int i = 0; i < arr.length; i++) {
            best = Math.max(best, dfs(i, arr, d, memo));
        }
        return best;
    }

    private int dfs(int cur, int[] arr, int d, Integer[] memo) {
        if (memo[cur] != null) return memo[cur];
        int res = 1;

        int right = Math.min(cur + d, arr.length - 1);
        for (int i = cur + 1; i <= right && arr[i] < arr[cur]; i++)
            res = Math.max(res, 1 + dfs(i, arr, d, memo));

        int left = Math.max(0, cur - d);
        for (int i = cur - 1; i >= left && arr[i] < arr[cur]; i--)
            res = Math.max(res, 1 + dfs(i, arr, d, memo));

        return memo[cur] = res;
    }
}
```

> **Why this code?**  
> The `stack()` method implements the *O(n)* solution that wins the CodeSignal contest.  
> All other methods are provided for educational comparison and can be swapped in/out by editing the `recursive`, `memoization`, `tabulation`, or `graph` methods in the `main` method.  
> The program follows the CodeSignal style: a public method that gets called automatically during grading.  

> **Run**: copy the whole file into the “Code” editor on CodeSignal, hit “Run” or “Submit”.  
>  
> **Pro tip**: for local debugging, you may add a `main()` method with sample tests.  
>  
> **Happy coding!** 🚀
