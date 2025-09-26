---
title: LeetCode 488. Zuma Game - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 Zuma Game – 488 on LeetCode  
> **Hard** – 5‑color board, 5‑ball hand, find the minimum insertions to clear the board

---

## TL;DR
| Language | Time  | Space | Key idea |
|----------|-------|-------|----------|
| **Java** | O(∑ *state* × *branch*) | O(∑ *state*) | DFS + memo + board compression |
| **Python** | O(∑ *state* × *branch*) | O(∑ *state*) | Recursion + lru_cache + tuple board |
| **C++** | O(∑ *state* × *branch*) | O(∑ *state*) | DFS + unordered_map + string board |

> **Result** – Minimal insertions (or `-1` if impossible).

---

## 1️⃣ Problem Restatement

We have a 1‑D board of colored balls (`R Y B G W`) and a hand of balls.  
A turn consists of

1. **Insert** any hand ball between two existing balls (or at an end).
2. **Collapse**: any contiguous group of 3+ same‑color balls disappears; new groups may form and collapse recursively.
3. **Repeat** until the board is empty (win) or hand is exhausted (lose).

Return the minimal number of insertions needed to clear the board, or `-1` if impossible.

**Constraints**

- `1 ≤ board.length ≤ 16`
- `1 ≤ hand.length ≤ 5`
- No initial group of 3+ exists on the board.

---

## 2️⃣ Why This Is “Hard”

| Factor | Why it matters |
|--------|----------------|
| **Exponential Search Space** | Each insertion can happen at *any* gap → branching factor up to 17, depth ≤ 5 → 17⁵ ≈ 1 400 000. |
| **State Explosion** | Board configurations can be huge; naive DFS revisits same states. |
| **Pruning Rules** | We must identify when a move is useless (e.g., inserting a color that never helps). |
| **Memoization Overlap** | Two different sequences may produce the same `(board, hand)` state. |
| **Language Variants** | Java: need custom hashing; Python: recursion limits; C++: speed vs. memory. |

---

## 3️⃣ Core Idea – DFS + Memo + Board Compression

1. **State Representation**  
   - **Board** – compressed as a string.  
   - **Hand** – an array of size 5 (`R Y B G W`) that tracks remaining counts.  
   - Encode state as `board#handString` (e.g., `"WWRBB#21200"`).  
2. **Recursive DFS**  
   - For every *gap* on the board, try to insert a ball of the same color as one of the neighboring balls (otherwise no collapse possible).  
   - After insertion, **collapse** the board: repeatedly remove any 3+ same‑color streaks.  
   - Recurse on the new board and updated hand.  
   - Keep track of minimal steps.  
3. **Memoization**  
   - Use a hash map (`Map<String,Integer> memo`) to cache the best answer for a given state.  
   - If a state has been solved before, return the cached value.  
4. **Pruning**  
   - If no hand ball matches any board color, skip.  
   - If a color needs `k` balls to collapse but the hand has fewer, skip.  
   - Early exit if the board becomes empty.  

The key is that the board is *short* (≤16) and the hand is tiny (≤5), so we can afford a compact DFS.

---

## 4️⃣ Code Implementations

### 4.1 Java

```java
import java.util.*;

public class Solution {
    // 0:R 1:Y 2:B 3:G 4:W
    private static final char[] COLORS = {'R', 'Y', 'B', 'G', 'W'};
    private Map<String, Integer> memo = new HashMap<>();

    public int findMinStep(String board, String hand) {
        int[] handCnt = new int[5];
        for (char c : hand.toCharArray())
            handCnt[charIdx(c)]++;

        return dfs(board, handCnt);
    }

    private int dfs(String board, int[] handCnt) {
        if (board.isEmpty()) return 0;
        String key = board + "#" + handCntToStr(handCnt);
        if (memo.containsKey(key)) return memo.get(key);

        int ans = Integer.MAX_VALUE;
        // For every gap position
        for (int i = 0; i <= board.length(); i++) {
            char left = (i == 0) ? 'X' : board.charAt(i - 1);
            char right = (i == board.length()) ? 'X' : board.charAt(i);
            // Only insert a ball that matches at least one neighbor
            for (int color = 0; color < 5; color++) {
                if (handCnt[color] == 0) continue;
                char c = COLORS[color];
                if (c != left && c != right) continue;

                handCnt[color]--;
                String after = collapse(board.substring(0, i) + c + board.substring(i));
                int res = dfs(after, handCnt);
                if (res != -1) ans = Math.min(ans, res + 1);
                handCnt[color]++;   // backtrack
            }
        }

        memo.put(key, ans == Integer.MAX_VALUE ? -1 : ans);
        return memo.get(key);
    }

    private String collapse(String s) {
        StringBuilder sb = new StringBuilder(s);
        boolean changed = true;
        while (changed) {
            changed = false;
            int n = sb.length();
            int i = 0;
            while (i < n) {
                int j = i + 1;
                while (j < n && sb.charAt(j) == sb.charAt(i)) j++;
                if (j - i >= 3) {          // remove streak
                    sb.delete(i, j);
                    changed = true;
                    n = sb.length();
                    i = Math.max(0, i - 1); // re‑check previous position
                } else {
                    i = j;
                }
            }
        }
        return sb.toString();
    }

    private int charIdx(char c) {
        switch (c) {
            case 'R': return 0;
            case 'Y': return 1;
            case 'B': return 2;
            case 'G': return 3;
            case 'W': return 4;
            default: throw new IllegalArgumentException();
        }
    }

    private String handCntToStr(int[] cnt) {
        return String.format("%d%d%d%d%d", cnt[0], cnt[1], cnt[2], cnt[3], cnt[4]);
    }
}
```

#### Why it Works

- **Memoization** guarantees each unique `(board, hand)` pair is processed once.
- **Gap filtering** (matching a neighbor) cuts off useless branches.
- **Collapse** is performed iteratively until stable, ensuring the board is always canonical for the memo key.

---

### 4.2 Python

```python
from functools import lru_cache
from collections import Counter

class Solution:
    COLORS = 'RYBGW'

    def findMinStep(self, board: str, hand: str) -> int:
        hand_cnt = [hand.count(c) for c in self.COLORS]

        @lru_cache(maxsize=None)
        def dfs(b: str, h: str) -> int:
            if not b:
                return 0
            hand_cnt = [int(h[i]) for i in range(5)]
            best = float('inf')

            # Scan every insertion point
            for i in range(len(b) + 1):
                left = b[i-1] if i else 'X'
                right = b[i] if i < len(b) else 'X'

                for c in self.COLORS:
                    if hand_cnt[self.COLORS.index(c)] == 0:
                        continue
                    if c != left and c != right:
                        continue

                    # Insert
                    new_board = b[:i] + c + b[i:]
                    new_board = collapse(new_board)

                    hand_cnt[self.COLORS.index(c)] -= 1
                    res = dfs(new_board, ''.join(map(str, hand_cnt)))
                    hand_cnt[self.COLORS.index(c)] += 1

                    if res != -1:
                        best = min(best, res + 1)

            return -1 if best == float('inf') else best

        return dfs(board, ''.join(map(str, hand_cnt)))

def collapse(s: str) -> str:
    """Repeatedly remove streaks of 3+ same color."""
    stack = []
    i = 0
    n = len(s)
    while i < n:
        j = i + 1
        while j < n and s[j] == s[i]:
            j += 1
        if j - i >= 3:
            i = j  # skip this block
        else:
            stack.append(s[i:j])
            i = j
    new_s = ''.join(stack)
    return collapse(new_s) if new_s != s else new_s
```

> **Tip:** Python recursion depth is not a problem here because depth ≤ 5 (hand size). The `collapse` function is tail‑recursive; it’s safe and fast.

---

### 4.3 C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
    const string COLORS = "RYBGW";
    unordered_map<string, int> memo;

    // Collapse board after insertion
    string collapse(const string &s) {
        string cur = s;
        bool changed = true;
        while (changed) {
            changed = false;
            int n = cur.size();
            string next;
            for (int i = 0; i < n; ) {
                int j = i + 1;
                while (j < n && cur[j] == cur[i]) j++;
                if (j - i >= 3) {
                    changed = true;          // remove this block
                } else {
                    next += cur.substr(i, j - i);
                }
                i = j;
            }
            cur.swap(next);
        }
        return cur;
    }

    int dfs(string board, vector<int> &hand) {
        if (board.empty()) return 0;
        string key = board + "#" + toKey(hand);
        if (memo.count(key)) return memo[key];

        int best = INT_MAX;
        int len = board.size();

        for (int i = 0; i <= len; ++i) {
            char left = (i == 0) ? '?' : board[i-1];
            char right = (i == len) ? '?' : board[i];

            for (int c = 0; c < 5; ++c) {
                if (!hand[c]) continue;
                char color = COLORS[c];
                if (color != left && color != right) continue;

                hand[c]--;
                string next = collapse(board.substr(0, i) + color + board.substr(i));
                int res = dfs(next, hand);
                hand[c]++;

                if (res != -1) best = min(best, res + 1);
            }
        }

        memo[key] = (best == INT_MAX) ? -1 : best;
        return memo[key];
    }

    string toKey(const vector<int> &hand) {
        string k;
        for (int v : hand) k += char('0' + v);
        return k;
    }

public:
    int findMinStep(string board, string hand) {
        vector<int> cnt(5, 0);
        for (char c : hand) cnt[COLORS.find(c)]++;
        return dfs(board, cnt);
    }
};
```

---

## 5️⃣ What Went Right? (The Good)

| ✅ | Explanation |
|---|-------------|
| **Compact State** | Board + hand counts fully capture game status. |
| **Memoization** | Avoids exponential blow‑up – each state visited once. |
| **Gap Filtering** | Only insert colors that can trigger a collapse → fewer branches. |
| **Iterative Collapse** | Board becomes canonical; same configuration always has same key. |
| **Language‑Specific Optimizations** | Java: custom hash key; Python: `lru_cache`; C++: `unordered_map` with string key. |

---

## 6️⃣ Where It Could Be Smarter? (The Bad)

| ⚠️ | Issue | Fix |
|---|-------|-----|
| **Unnecessary Key Re‑computation** | Building a new string key each recursion call can be heavy. | Pre‑compute key string for hand once, reuse it. |
| **Collapse Complexity** | While the board is short, collapse runs in O(N²) if done naively. | Use two‑pointer stack approach to O(N). |
| **Large Hand** | The current pruning assumes hand ≤ 5. If hand grows, algorithm may need further pruning (e.g., counting needed balls for each color). |
| **Thread‑Safety** | Memo map is not thread‑safe. In contests, single thread is fine. |

---

## 6️⃣ Common Pitfalls & Edge Cases

| Issue | Why it happens | Fix |
|-------|----------------|-----|
| **Empty Hand** | If hand has zero cards for all colors that appear in board, DFS returns `-1` immediately. | Check early and return `-1`. |
| **Circular Collapse** | Removing one block may create another of the same color at the boundary. | Iterative collapse loop (changed flag) ensures all cascades are handled. |
| **Duplicate Keys** | Minor difference in hand order can produce the same state. | Hand key is a 5‑digit string; order is fixed by COLORS. |
| **Python Recursion Depth** | Depth is bounded by hand size (≤5), so safe. | If hand size grew, consider iterative DFS or increase recursion limit. |

---

## 6️⃣ Summary & Takeaways

- **Zoning in on key operations** (insert at gap + collapse) reduces the problem to a small DFS.
- **Canonical representation** (stable collapsed board) lets memoization be effective.
- **Time Complexity**: \(O(3^{H} \times 5^{H})\) ≈ \(O(10^3)\) for worst case, thanks to memo.
- **Space Complexity**: dominated by memo map – at most a few thousand entries.

---

## 7️⃣ Final Thoughts

Whether you’re coding in **Java**, **Python**, or **C++**, the core idea stays the same: treat the game as a state‑search problem and use memoization to prune. The Zuma‑like game in LeetCode 488 is a great exercise in *state compression*, *dynamic programming*, and *recursive backtracking*.

Happy coding! 🚀

--- 

**Keywords:** Zuma game, LeetCode 488, findMinStep, DFS, memoization, board collapse, hand count, Java LRU cache, Python lru_cache, C++ unordered_map, algorithm design.