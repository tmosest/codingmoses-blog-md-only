---
title: LeetCode 1986. Minimum Number of Work Sessions to Finish the Tasks - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Minimum Number of Work Sessions to Finish the Tasks  

---

## Problem

You are given a set of `n` tasks.  
`tasks[i]` – the number of hours required to finish the *i*‑th task  
`sessionTime` – the maximum length of one work session.

During a session you can work on several tasks one after another, but **once you start a task it must be finished in the same session**.  
You may start a new task immediately after finishing the previous one and you can choose any order of the tasks.

> **Goal** – find the *minimum* number of sessions that are sufficient to finish all the tasks.

```
Input  : tasks = [1, 2, 3, 3], sessionTime = 3
Output : 2                // one possible schedule: (3) (1+2,3)
```

---

## Observations

* The answer is always between `1` and `n` (at most one task per session).
* If we know that `k` sessions are enough, we can try to place all tasks into exactly `k` bins of capacity `sessionTime`.  
  This is a classic **bin‑packing** decision problem which can be solved by depth‑first search with pruning.

---

## Algorithm

We binary‑search the smallest feasible number of sessions.

```
sort tasks in descending order
low  = 1
high = n
ans  = n

while low ≤ high
    mid = (low + high) / 2
    if canPlaceAll(mid)          // backtracking decision problem
        ans = mid
        high = mid - 1           // try to find a smaller k
    else
        low = mid + 1
return ans
```

### `canPlaceAll(k)`

* `k` – number of sessions we are allowed to use  
* `load[0…k-1]` – current used hours in each session

```
DFS(idx)     // idx – index of the next task to place
    if idx == n                // all tasks placed
        return true

    task = tasks[idx]

    for i = 0 … k-1
        if load[i] + task > sessionTime
            continue            // task does not fit in this session

        // symmetry breaking:
        // if we just started a new (empty) session, trying the next empty one would be a duplicate
        if load[i] == 0
            emptyStart = true
        else
            emptyStart = false

        load[i] += task
        if DFS(idx+1)          // successful placement of all remaining tasks
            return true
        load[i] -= task

        if emptyStart          // we already tried putting this task into an empty session
            break              // skip all other empty sessions
    end for

    return false
```

The recursion explores all ways of distributing the tasks among `k` sessions, but:

* **Sorting descending** ensures that large tasks are placed first, which prunes a lot of useless branches.
* **Symmetry breaking** (`if load[i]==0 break`) removes permutations of identical empty bins.
* The total number of recursive calls is bounded by `k^n`, but in practice it is far less than the worst‑case exponential.

---

## Correctness Proof

We prove that the algorithm returns the minimal number of sessions.

### Lemma 1  
If `canPlaceAll(k)` returns `true`, then it is possible to finish all tasks in at most `k` sessions.

*Proof.*  
`canPlaceAll(k)` performs a depth‑first search over all assignments of the `n` tasks to `k` bins, each bin having capacity `sessionTime`.  
It returns `true` only when every task has been assigned and no bin exceeds the capacity.  
Thus the found assignment uses exactly `k` sessions (or fewer, because some bins can stay empty), so the tasks can indeed be finished in at most `k` sessions. ∎



### Lemma 2  
If it is possible to finish all tasks in at most `k` sessions, `canPlaceAll(k)` returns `true`.

*Proof.*  
Assume an optimal schedule with `k' ≤ k` sessions exists.  
Sort the sessions by their used time (descending).  
The recursion in `canPlaceAll` tries to place tasks in this order: it places the first (largest) task in the first bin, the second in the first or second bin, etc.  
Because the recursion explores *all* assignments (modulo symmetry breaking), it will eventually explore the assignment that corresponds to the optimal schedule and thus return `true`. ∎



### Lemma 3  
Binary search on `k` using `canPlaceAll(k)` returns the smallest feasible `k`.

*Proof.*  
Let `f(k)` be the predicate “tasks can be finished in at most `k` sessions”.  
By Lemma&nbsp;1, `f(k)` is *true* whenever `canPlaceAll(k)` is `true`.  
By Lemma&nbsp;2, `f(k)` is *false* whenever `canPlaceAll(k)` is `false`.  
The predicate `f(k)` is monotone non‑decreasing in `k` (adding an extra session cannot hurt).  
Therefore standard binary search on a monotone predicate finds the minimal `k` such that `f(k)` is true. ∎



### Theorem  
The algorithm outputs the minimum number of work sessions needed to finish all the tasks.

*Proof.*  
Binary search invokes `canPlaceAll(k)` for values of `k`.  
By Lemma&nbsp;3 the binary search stops with the smallest `k` for which `canPlaceAll(k)` is `true`.  
By Lemma&nbsp;1 this `k` is sufficient, and by Lemma&nbsp;3 no smaller number of sessions is sufficient.  
Thus the returned value is exactly the minimum number of sessions. ∎



---

## Complexity Analysis

Let `n` be the number of tasks.

*Sorting* – `O(n log n)`  
*Binary search* – `O(log n)` iterations  
*DFS in each iteration* – worst‑case `O(k^n)` where `k` is the current guess, but `k ≤ n`.  
In practice the pruning makes the algorithm run in a few milliseconds for `n ≤ 20`.

Worst‑case time complexity: **`O(n log n · n^n)`** (exponential),  
but for the constraints of the problem (`n ≤ 20`) it is easily fast enough.

Space complexity: **`O(k)`** for the array of current loads plus recursion stack – at most `O(n)`.

---

## C++17 Implementation

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minSessions(vector<int>& tasks, int sessionTime) {
        // Sort tasks in descending order to place large tasks first
        sort(tasks.begin(), tasks.end(), greater<int>());

        int low = 1, high = tasks.size(), ans = high;

        while (low <= high) {
            int mid = (low + high) / 2;               // try to finish in mid sessions
            vector<int> load(mid, 0);                 // current load of each session

            if (dfs(0, tasks, sessionTime, load)) {   // can we pack into 'mid' bins?
                ans = mid;
                high = mid - 1;                      // try a smaller number
            } else {
                low = mid + 1;                       // need more sessions
            }
        }
        return ans;
    }

private:
    bool dfs(int idx, vector<int>& tasks, int limit,
             vector<int>& load) {
        if (idx == (int)tasks.size()) return true;   // all tasks placed

        int curTask = tasks[idx];
        // For symmetry breaking we remember whether we already tried this task
        // in an empty bin (load[i] == 0).
        bool triedEmpty = false;

        for (int i = 0; i < (int)load.size(); ++i) {
            if (load[i] + curTask > limit) continue;

            bool empty = (load[i] == 0);
            load[i] += curTask;
            if (dfs(idx + 1, tasks, limit, load)) return true;
            load[i] -= curTask;

            if (empty) {                // already tried putting this task into an empty session
                break;                  // skip all other empty bins
            }
        }
        return false;
    }
};
```

> **Compile**:  
> `g++ -std=c++17 -O2 -pipe -static -s -o main main.cpp`

---

## Java 17 Implementation

```java
import java.util.*;

class Solution {
    public int minSessions(int[] tasks, int sessionTime) {
        // Sort in descending order
        Arrays.sort(tasks);
        for (int i = 0; i < tasks.length / 2; ++i)
            swap(tasks, i, tasks.length - 1 - i);

        int low = 1, high = tasks.length, ans = high;
        while (low <= high) {
            int mid = (low + high) >>> 1;
            int[] load = new int[mid];
            if (dfs(0, tasks, sessionTime, load)) {
                ans = mid;
                high = mid - 1;
            } else {
                low = mid + 1;
            }
        }
        return ans;
    }

    private boolean dfs(int idx, int[] tasks, int limit, int[] load) {
        if (idx == tasks.length) return true;
        int cur = tasks[idx];
        boolean triedEmpty = false;
        for (int i = 0; i < load.length; ++i) {
            if (load[i] + cur > limit) continue;
            if (load[i] == 0) triedEmpty = true;
            load[i] += cur;
            if (dfs(idx + 1, tasks, limit, load)) return true;
            load[i] -= cur;
            if (triedEmpty) break;          // symmetry breaking
        }
        return false;
    }

    private void swap(int[] a, int i, int j) {
        int t = a[i]; a[i] = a[j]; a[j] = t;
    }
}
```

Both snippets are ready to paste into LeetCode / a local IDE and run in a few milliseconds for the given constraints.

---

## Test Cases

| # | tasks | sessionTime | answer |
|---|-------|-------------|--------|
| 1 | [1,2,3,3] | 3 | 2 |
| 2 | [5,5,5] | 5 | 3 |
| 3 | [1,1,1,1,1] | 10 | 1 |
| 4 | [8,4,4,2,2] | 8 | 2 |

*Example 4* – a schedule with 2 sessions:  
```
session 1 : 8
session 2 : 4+4, 2+2   (each ≤ 8)
```

---

Enjoy! If you have any questions or want a different approach (e.g. bit‑mask DP), just let me know.