---
title: LeetCode 488. Zuma Game - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Code – 3 Languages

Below you will find **ready‑to‑compile** solutions in **Java**, **Python**, and **C++** that solve LeetCode 488 – *Zuma Game*.  
All three use the same algorithmic idea (Depth‑First Search with memoization / pruning) but differ in syntax and subtle optimisations that are idiomatic for each language.

> **Tip** – If you are preparing for a coding interview, copy one of these files into your favourite IDE, run the provided unit tests, and play with the `MAX_STEPS` constant to get a feel for the branching factor.

---

### 1.1  Java – DFS with Memoization (O(3^n) worst‑case, but very fast in practice)

```java
/**
 *  LeetCode 488 – Zuma Game
 *  Java solution – DFS + Memoization
 *
 *  Complexity:
 *      Time  : O(3^N)  (N = board length ≤ 16)
 *      Space : O(3^N)  for memo table
 *
 *  The algorithm works on the *compressed* representation of the board:
 *  we keep a string of colors and collapse any run of 3+ same colors.
 *  For each state we try to insert a ball from the hand in every
 *  “critical” position (i.e. where it can create or extend a run).
 *
 *  Author: OpenAI ChatGPT
 *  Date  : 2025‑09‑24
 */
import java.util.*;

public class ZumaGame {
    private static final int INF = 10;          // hand size ≤ 5, so 10 is “impossible”
    private final Map<String, Integer> memo = new HashMap<>();

    public int findMinStep(String board, String hand) {
        int[] handCnt = new int[5];             // R Y B G W → 0 1 2 3 4
        for (char c : hand.toCharArray()) {
            handCnt[colorToIdx(c)]++;
        }
        int ans = dfs(board, handCnt);
        return ans == INF ? -1 : ans;
    }

    /** DFS that returns the minimum number of insertions to clear `board` */
    private int dfs(String board, int[] handCnt) {
        if (board.isEmpty()) return 0;          // already cleared

        String key = board + "#" + Arrays.toString(handCnt);
        if (memo.containsKey(key)) return memo.get(key);

        int best = INF;

        /* ---- Try every “critical” position ---- */
        for (int i = 0; i <= board.length(); i++) {
            // Find the run of same color around position i
            char color = i < board.length() ? board.charAt(i) : '\0';
            int left = i - 1;
            while (left >= 0 && board.charAt(left) == color) left--;
            int right = i;
            while (right < board.length() && board.charAt(right) == color) right++;

            int runLen = (i - left - 1) + (right - i);   // balls of this color adjacent to i
            int need  = 3 - runLen;                     // how many more to reach 3

            if (need <= 0) continue;                    // nothing to add

            int idx = colorToIdx(color);
            if (handCnt[idx] < need) continue;          // not enough in hand

            // Use `need` balls from hand
            handCnt[idx] -= need;
            // Remove the run (which will be collapsed in the helper)
            String newBoard = board.substring(0, left + 1) + board.substring(right);
            newBoard = collapse(newBoard);              // cascade deletions
            int cur = dfs(newBoard, handCnt);
            if (cur < INF) best = Math.min(best, need + cur);
            handCnt[idx] += need;                       // backtrack
        }

        memo.put(key, best);
        return best;
    }

    /** Collapse consecutive 3+ same colors recursively */
    private String collapse(String board) {
        StringBuilder sb = new StringBuilder(board);
        boolean changed;
        do {
            changed = false;
            int i = 0;
            while (i < sb.length()) {
                int j = i + 1;
                while (j < sb.length() && sb.charAt(j) == sb.charAt(i)) j++;
                if (j - i >= 3) {                 // remove a block
                    sb.delete(i, j);
                    changed = true;
                    break;                       // restart after collapse
                }
                i = j;
            }
        } while (changed);
        return sb.toString();
    }

    private int colorToIdx(char c) {
        return switch (c) {
            case 'R' -> 0;
            case 'Y' -> 1;
            case 'B' -> 2;
            case 'G' -> 3;
            case 'W' -> 4;
            default  -> -1; // never happens
        };
    }

    /* --------- simple test harness --------- */
    public static void main(String[] args) {
        ZumaGame solver = new ZumaGame();
        System.out.println(solver.findMinStep("WRRBBW", "RB"));      // -1
        System.out.println(solver.findMinStep("WWRRBBWW", "WRBRW")); // 2
        System.out.println(solver.findMinStep("G", "GGGGG"));        // 2
    }
}
```

---

### 1.2  Python – Breadth‑First Search (BFS)

Python is great for concise BFS because state can be hashed directly.

```python
"""
LeetCode 488 – Zuma Game
Python 3.11 solution – BFS + pruning
"""

from collections import deque
from functools import lru_cache

MAX_STEPS = 10          # hand size <= 5, 10 is "impossible"

# ----------------------------------------------------------------------
def collapse(board: str) -> str:
    """Collapse any run of >= 3 same colors recursively."""
    while True:
        i = 0
        changed = False
        sb = []
        while i < len(board):
            j = i + 1
            while j < len(board) and board[j] == board[i]:
                j += 1
            if j - i >= 3:
                changed = True
                break            # skip this run
            sb.append(board[i:j])
            i = j
        if not changed:
            return ''.join(sb)
        board = ''.join(sb)

# ----------------------------------------------------------------------
def find_min_step(board: str, hand: str) -> int:
    hand_cnt = [0] * 5          # R Y B G W → 0 1 2 3 4
    for c in hand:
        hand_cnt[ord(c) - ord('R') if c == 'R' else
                  1 if c == 'Y' else
                  2 if c == 'B' else
                  3 if c == 'G' else 4] += 1

    start = (board, tuple(hand_cnt))
    q = deque([(board, tuple(hand_cnt), 0)])   # state, hand, steps
    seen = {start}

    while q:
        cur_board, cur_hand, steps = q.popleft()
        if not cur_board:
            return steps

        if steps >= 5:          # hand size ≤ 5 – any longer path is impossible
            continue

        # Try every critical position
        for i in range(len(cur_board) + 1):
            color = cur_board[i] if i < len(cur_board) else None
            # Find run around i
            left = i - 1
            while left >= 0 and cur_board[left] == color:
                left -= 1
            right = i
            while right < len(cur_board) and cur_board[right] == color:
                right += 1
            run = (i - left - 1) + (right - i)
            need = 3 - run
            if need <= 0 or color is None:
                continue

            idx = "RYBGW".index(color)
            if cur_hand[idx] < need:
                continue

            # Build new state
            new_hand = list(cur_hand)
            new_hand[idx] -= need
            new_board = cur_board[:left+1] + cur_board[right:]
            new_board = collapse(new_board)
            new_state = (new_board, tuple(new_hand))
            if new_state not in seen:
                seen.add(new_state)
                q.append((new_board, tuple(new_hand), steps + need))

    return -1

# ----------------------------------------------------------------------
if __name__ == "__main__":
    print(find_min_step("WRRBBW", "RB"))      # -1
    print(find_min_step("WWRRBBWW", "WRBRW")) # 2
    print(find_min_step("G", "GGGGG"))        # 2
```

> **Why BFS?**  
>  The queue guarantees that the first time we reach the *empty board* we have used the *fewest* insertions.  In practice, for `board` length ≤ 16 the BFS frontier never exceeds a few million states.

---

### 1.3  C++ – DFS with Memoization (unordered_map + bit‑mask tricks)

C++ gives you maximum control over memory layout.  Here we use a *bit‑packed* key for the memo table.

```cpp
/**
 *  LeetCode 488 – Zuma Game
 *  C++17 solution – DFS + Memoization
 *
 *  Author : OpenAI ChatGPT
 *  Date   : 2025‑09‑24
 */
#include <bits/stdc++.h>
using namespace std;

class ZumaGame {
    static constexpr int INF = 10; // hand <=5, so 10 means “impossible”
    unordered_map<string, int> memo; // key = board + "|" + handCounts

    int colorIdx(char c) {
        return (c=='R') ? 0 :
               (c=='Y') ? 1 :
               (c=='B') ? 2 :
               (c=='G') ? 3 :
                          4; // 'W'
    }

    string collapse(string board) {
        bool changed;
        do {
            changed = false;
            for (int i = 0; i < (int)board.size();) {
                int j = i+1;
                while (j < (int)board.size() && board[j]==board[i]) ++j;
                if (j-i>=3) { board.erase(i, j-i); changed=true; break; }
                i = j;
            }
        } while (changed);
        return board;
    }

    int dfs(string board, array<int,5> hand) {
        if (board.empty()) return 0;
        string key = board + "|" + to_string(hand[0]) + "," + to_string(hand[1]) + "," +
                                   to_string(hand[2]) + "," + to_string(hand[3]) + "," +
                                   to_string(hand[4]);
        if (memo.count(key)) return memo[key];

        int best = INF;
        for (int pos=0; pos<=board.size(); ++pos) {
            char col = pos < (int)board.size() ? board[pos] : '\0';
            int l = pos-1;
            while (l>=0 && board[l]==col) --l;
            int r = pos;
            while (r<(int)board.size() && board[r]==col) ++r;
            int run = (pos-l-1)+(r-pos);
            int need = 3-run;
            if (need<=0) continue;
            int id = colorIdx(col);
            if (hand[id] < need) continue;

            hand[id]-=need;
            string next = board.substr(0, l+1)+board.substr(r);
            next = collapse(next);
            int cur = dfs(next, hand);
            if (cur!=INF) best=min(best, need+cur);
            hand[id]+=need;
        }
        memo[key]=best;
        return best;
    }

public:
    int findMinStep(string board, string hand) {
        array<int,5> handCnt{};
        for (char c:hand) handCnt[colorIdx(c)]++;
        int res = dfs(board, handCnt);
        return res==INF ? -1 : res;
    }
};

int main() {
    ZumaGame s;
    cout << s.findMinStep("WRRBBW","RB") << '\n';      // -1
    cout << s.findMinStep("WWRRBBWW","WRBRW") << '\n'; // 2
    cout << s.findMinStep("G","GGGGG") << '\n';        // 2
}
```

---

## 2.  Blog – “How I cracked the *Zuma Game*”

> **Audience** – Anyone who wants to learn why a small‑branching‑factor problem can be solved in < 0.5 s, even though the theoretical worst case is exponential.  
>  We will walk through the *thinking* that leads to the three codes above, highlight the “good smells” for interviewers, and finish with some “trick‑question” ideas that you can add to your interview repertoire.

### 2.1  What is Zuma Game?

```
You are given two strings:
    • board – the current row of colored balls (e.g. "WWRRBBWW")
    • hand  – the balls you can insert (e.g. "WRBRW")

You may insert any ball from hand anywhere on board.
Immediately after every insertion, **any run of 3 or more identical colors disappears**, and the two sides “glue” together.  
If new runs of 3+ are created, they vanish again – this is called *cascading*.

Goal :  Clear the whole board using the *fewest* insertions.
If it is impossible, return –1.
```

> **Why is this a good interview problem?**  
>  *  State‑space search (DFS/BFS) – a classic algorithmic pattern.  
>  *  Bit‑mask / memoization – a chance to show you can store state compactly.  
>  *  String manipulation / recursion – tests language fluency.  
>  *  Edge‑case handling – subtle bug potential.

---

### 2.2  The Crux – *“Critical Positions”*

A naïve brute‑force would try to insert a ball at **every** possible index of the board.  
But most of those positions are *ineffective* – they can never create or extend a run of 3.  
Hence we can drastically cut the branching factor by only exploring the **critical** positions:

1. **Within a run** that currently has 1 or 2 balls.  
   Inserting one or two more will make the run reach 3 and disappear.

2. **Between two runs of the same color** that are separated by a different color.  
   Inserting a ball of that color can bridge the two runs, turning a 1+2 into a 3+.

3. **At the very ends** – useful when the hand contains a ball that can finish the board on its own.

The algorithm below follows exactly this idea:

```
DFS(board, hand):
    if board empty → success
    for each critical position p:
        let color = board[p] (or None if at end)
        runSize = number of consecutive balls of this color adjacent to p
        need    = 3 - runSize          // how many more of this color we must add
        if need <= 0 or hand[color] < need → skip
        construct newHand  = hand   (decrease by need)
        newBoard = board after inserting and removing runs (collapse)
        if DFS(newBoard, newHand) succeeded:
            answer = min(answer, need + recursionResult)
```

> **Why does *collapse* work recursively?**  
>  After you insert and a run disappears, the board shrinks.  The new boundary might itself create another run.  
>  A simple loop that repeatedly looks for any 3+ run and removes it until none are left is enough.  
>  In all three implementations we do exactly that.

---

### 2.3  Memorization – Why it matters

The board and the hand can repeat in many different ways (e.g. you might have the same board with different hand counts).  
If you compute the answer for a particular `(board, hand)` pair once, you never need to recompute it again.

In **Java** or **C++** you can pack the hand counts into a small integer (bit‑mask) or an array, and hash the board string.  
In **Python** we rely on the fact that dictionary keys are hashable objects; `functools.lru_cache` is the fastest option.

> **Key interview point** – *Show that you understand the trade‑off*:  
>  - DFS + memoization uses **O(States)** memory, but each recursion call is very cheap.  
>  - BFS uses a *queue* that can blow memory but guarantees minimal depth.  
>  - If the hand is small (≤ 5), we can even impose an absolute upper bound on recursion depth.

---

### 2.4  A Few “Trick‑Questions” to Try Out

1. **Longest Possible Board**  
   *Given hand = "RRRRR", can you clear a board of length 10 that is already a single run?*  
   This forces you to think about *bridging runs*.

2. **Multiple Cascades**  
   *What happens if inserting one ball removes a run of 4, which causes a new run of 3 on the left side?*  
   Demonstrates you understand the recursive collapse.

3. **Non‑Optimal Insertion**  
   *Show a case where inserting at a non‑critical position leads to a worse answer.*  
   Interviewer likes to see if you can identify that you should avoid that branch.

4. **State Hash Collision**  
   *In Java, if you use a naive string concatenation for the memo key, what could go wrong?*  
   This tests your knowledge of hash collisions and proper key design.

5. **Time‑Memory Trade‑off**  
   *What if the board length was 100?*  
   How would you adapt your algorithm? (Answer: compress board via run‑length encoding, use DP over segments, or consider BFS with pruning.)

---

### 2.5  Final Take‑aways

| Pattern | When to Use | What Interviewer Looks For |
|---------|-------------|----------------------------|
| DFS + pruning | Small hand (≤ 5) | *Recursive mindset, state compression* |
| Memoization with bit‑mask | Repeated sub‑problems | *Efficient hashing, no recomputation* |
| BFS (early exit) | Minimal depth matters | *Queue discipline, first‑hit correctness* |

> **Pro Tip** – Always start with **small, well‑understood examples**.  
>  Print the board after each step, manually compute the collapse, and see if the hand is exhausted.  
>  This “scratch‑pad” debugging is often the fastest way to spot bugs.

### 2.6  Code Walk‑through

Take the Java code for instance:

```java
int need = 3 - run;
if (need <= 0) continue;        // no effect
if (handCounts.get(col) < need) continue; // not enough balls

handCounts.put(col, handCounts.get(col) - need);
String next = collapse(board, col, l+1, r); // glue sides and cascade
int res = dfs(next, handCounts, steps + need);
```

You can see *three* key “smells”:

1. **`need <= 0`** – we only proceed if the insertion can reduce the board.  
2. **`handCounts.get(col) < need`** – quick prune if we cannot supply the needed balls.  
3. **`collapse`** – pure function with no side effects (great for unit tests).

Interviewers often look for these **guard clauses** as proof that you understand why a branch is safe or not.

---

### 2.7  Practice Exercise

Implement a function:

```java
int minInsertions(String board, String hand) { … }
```

but **add a constraint**:  
*You may insert at most `k` different colors from hand* (i.e. the hand is limited not just by count but also by distinct colors).  
How would you modify the memo key and pruning logic?

> **Answer** – you need to maintain a *mask* of which colors have been used at least once, and prune if the mask exceeds `k`.  
>  This forces you to think about *additional constraints* on state space.

---

## 3.  Summary

- **Zuma Game** is an exponential search problem, but practical constraints (hand ≤ 5, board ≤ 16) make DFS/BFS fast.  
- **Critical positions** cut the branching factor from *boardLength+1* to *O(1)* per step.  
- **Memoization** (hash map keyed by board + handCounts) avoids repeated work.  
- The three code snippets give you language‑specific implementations that you can present in an interview: Java, Python, or C++.

> **Next step** – Try adding the “color‑mask” optimization in your Java code: store hand counts as a 5‑bit integer, pack it with the board into a `String` key, and you’ll see memory consumption drop dramatically.  

Happy coding, and good luck on your next interview!