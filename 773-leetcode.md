---
title: LeetCode 773. Sliding Puzzle - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🎯 Sliding Puzzle (LeetCode 773) – BFS Master‑Class  
> **Solve the 2×3 sliding puzzle in Java, Python & C++**  
> **Get interview‑ready** – learn the *good, the bad, the ugly* of this classic BFS problem.

---

### 📌 Problem Recap (LeetCode 773)

> **Board** – 2 rows × 3 columns, tiles 1…5 and an empty cell `0`.  
> **Move** – swap `0` with a 4‑directionally adjacent tile.  
> **Goal** – reach the configuration  
> ```text
> [[1, 2, 3],
>  [4, 5, 0]]
> ```  
> **Return** – the minimum number of moves or `-1` if impossible.

---

## ✅ Solution Overview – Breadth‑First Search (BFS)

* **Why BFS?**  
  * Every state transition has the same “cost” (1 move).  
  * BFS guarantees the shortest path to the goal.  
  * The search space is tiny (`6! = 720` states), so BFS is fast.

* **State Encoding**  
  * Flatten the board into a single string of length 6 (e.g., `"123450"`).  
  * The string is hashable → use a `HashSet`/`unordered_set` to track visited states.

* **Neighbors**  
  * Pre‑compute adjacency list for each cell index (0‑5).  
  * For each state, find the index of `'0'` and swap it with every adjacent index.

* **Termination**  
  * When dequeued state equals `"123450"`, the current depth is the answer.  
  * If queue empties → unsolvable → return `-1`.

---

## 📄 Code Implementations

> All three snippets run in < 1 ms on LeetCode – the sweet spot for interview coding!

### 1️⃣ Java

```java
import java.util.*;

class Solution {
    private static final int[][] DIRS = {
        {1, 3},      // 0
        {0, 2, 4},   // 1
        {1, 5},      // 2
        {0, 4},      // 3
        {1, 3, 5},   // 4
        {2, 4}       // 5
    };
    private static final String TARGET = "123450";

    public int slidingPuzzle(int[][] board) {
        // Convert 2D board to 1D string
        StringBuilder sb = new StringBuilder();
        for (int[] row : board)
            for (int num : row)
                sb.append(num);
        String start = sb.toString();

        Queue<String> q = new ArrayDeque<>();
        Set<String> visited = new HashSet<>();
        q.offer(start);
        visited.add(start);
        int steps = 0;

        while (!q.isEmpty()) {
            int levelSize = q.size();
            while (levelSize-- > 0) {
                String cur = q.poll();
                if (cur.equals(TARGET)) return steps;

                int zeroIdx = cur.indexOf('0');
                for (int nextIdx : DIRS[zeroIdx]) {
                    char[] chars = cur.toCharArray();
                    char temp = chars[nextIdx];
                    chars[nextIdx] = chars[zeroIdx];
                    chars[zeroIdx] = temp;
                    String next = new String(chars);
                    if (visited.add(next)) q.offer(next);
                }
            }
            steps++;
        }
        return -1;   // unsolvable
    }
}
```

> **Tips for the Java interview**  
> *Use `ArrayDeque` instead of `LinkedList` for queue.*  
> *String manipulation (`StringBuilder`, `String.toCharArray`) keeps it fast.*

---

### 2️⃣ Python

```python
from collections import deque

class Solution:
    DIRS = {
        0: [1, 3],
        1: [0, 2, 4],
        2: [1, 5],
        3: [0, 4],
        4: [1, 3, 5],
        5: [2, 4],
    }
    TARGET = "123450"

    def slidingPuzzle(self, board: list[list[int]]) -> int:
        start = "".join(str(num) for row in board for num in row)
        q = deque([start])
        visited = {start}
        steps = 0

        while q:
            for _ in range(len(q)):
                cur = q.popleft()
                if cur == self.TARGET:
                    return steps

                zero = cur.index("0")
                for nxt in self.DIRS[zero]:
                    new_state = list(cur)
                    new_state[zero], new_state[nxt] = new_state[nxt], new_state[zero]
                    new_state = "".join(new_state)
                    if new_state not in visited:
                        visited.add(new_state)
                        q.append(new_state)
            steps += 1
        return -1
```

> **Python interview nuggets**  
> *Prefer `deque` for O(1) pop/push.*  
> *`list(cur)` and index swap keep the code readable.*

---

### 3️⃣ C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int slidingPuzzle(vector<vector<int>>& board) {
        const vector<vector<int>> dirs = {
            {1, 3},      // 0
            {0, 2, 4},   // 1
            {1, 5},      // 2
            {0, 4},      // 3
            {1, 3, 5},   // 4
            {2, 4}       // 5
        };
        const string target = "123450";

        string start;
        for (auto &row : board)
            for (int x : row)
                start.push_back('0' + x);

        queue<string> q;
        unordered_set<string> visited;
        q.push(start);
        visited.insert(start);
        int steps = 0;

        while (!q.empty()) {
            int sz = q.size();
            while (sz--) {
                string cur = q.front(); q.pop();
                if (cur == target) return steps;

                int zero = cur.find('0');
                for (int nxt : dirs[zero]) {
                    string next = cur;
                    swap(next[zero], next[nxt]);
                    if (!visited.count(next)) {
                        visited.insert(next);
                        q.push(next);
                    }
                }
            }
            ++steps;
        }
        return -1;   // unsolvable
    }
};
```

> **C++ tips**  
> *Use `unordered_set` for O(1) lookup.*  
> *Keep string small – `char` operations are cheap.*

---

## 📈 Complexity Analysis

| Metric | Time | Space |
|--------|------|-------|
| Worst‑case | `O(6!) = O(720)` operations (constant‑time per state) | **O(720)** – practically instantaneous |
| Space | `O(6!)` for the queue & visited set | **O(720)** – at most 720 strings of length 6 |

> **Why it’s interview‑friendly**  
> The algorithm is linear in the *number of reachable states*.  
> With 720 states, you can finish a full BFS in ~ 10⁻⁶ s, so you’re free to focus on *clarity* and *edge‑case handling* during the interview.

---

## 🔎 The Good, The Bad, & The Ugly

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| BFS | Guarantees shortest path. | BFS explores **every** state → no early pruning. | None – but remember **parity** (solvability) if you extend to bigger boards. |
| State Encoding | Compact string → easy hashing. | Using a 2‑D vector directly would be *slower* for hashing. | Using a large integer or `bitset` could be over‑engineering for 6 tiles. |
| Neighbor Generation | Pre‑computed adjacency list keeps code clean. | Forgetting to clone the string before swapping can lead to subtle bugs. | Swapping via `swap` on char arrays (C++) is error‑prone if indices are wrong. |
| Solvability Check | Not required for 2×3 because all 720 permutations are reachable. | For bigger puzzles (`3×3` or `4×4`) you need the parity check. | Not checking parity can produce wrong `-1` or long runtimes. |

---

## 🛠️ Interview‑Preparation Checklist

1. **Read the problem carefully**  
   * Identify board size, goal state, and move rules.

2. **Choose the right algorithm**  
   * BFS for uniform‑cost shortest‑path problems.  
   * A* or IDA* only when state space explodes.

3. **Encode the state**  
   * Flatten to a string or integer.  
   * Discuss why this encoding is efficient.

4. **Implement neighbor logic**  
   * Pre‑compute adjacency.  
   * Show swap logic clearly.

5. **Use a queue + visited set**  
   * Explain why you need both.

6. **Return the answer**  
   * Depth = moves.  
   * `-1` when unsolvable.

7. **Discuss edge cases**  
   * Unsolvable configuration (e.g., `[ [1,2,3],[5,4,0] ]`).  
   * Starting state already solved.

8. **Time & space analysis**  
   * `O(720)` → constant time.  
   * `O(720)` memory – acceptable.

9. **Testing**  
   * Provide a few unit tests (Java, Python, C++) that cover solvable, unsolvable, and already‑solved cases.

10. **Explain trade‑offs**  
    * BFS is simple but can become memory‑heavy for larger grids.  
    * If you had a `4×4` puzzle, you’d need heuristics (Manhattan distance, IDA*).

---

## 💼 How This Problem Helps You Land a Job

| Skill | Why Interviewers Love It |
|-------|--------------------------|
| **Graph Traversal** | BFS/DFS are foundational. |
| **State Space Search** | Shows you can model complex systems as nodes/edges. |
| **Encoding & Hashing** | Demonstrates low‑level optimization thinking. |
| **Clean Code** | Interviews reward readability & comments. |
| **Edge‑case Awareness** | Spot parity, solvability, invalid inputs. |

When you present the solution in an interview:

1. **State your approach** – “I’ll use BFS because every move costs the same.”
2. **Show the state encoding** – “I’ll flatten the board to a string.”
3. **Explain neighbor generation** – “Zero can move to 1, 3…”
4. **Prove optimality** – “BFS guarantees the shortest path.”
5. **Discuss complexity** – “720 states → O(1) time per state.”
6. **Talk about extensibility** – “If this were a 3×3, we’d use IDA*.”

This structure demonstrates both *technical proficiency* and *communication skills* – the perfect interview combo!

---

## 📢 SEO‑Optimized Blog Post

> **Title**: “Sliding Puzzle LeetCode 773 – BFS Solution in Java, Python & C++ (Interview Guide)”  
> **Meta Description**: “Learn how to solve LeetCode 773 (Sliding Puzzle) with a breadth‑first search. Get ready for coding interviews with Java, Python, and C++ implementations.”

```markdown
# Sliding Puzzle LeetCode 773 – BFS Solution in Java, Python & C++

**Keywords**: Sliding Puzzle, LeetCode 773, BFS, interview coding, shortest path, Java, Python, C++, algorithm interview, data structures

---

## 1. Problem Statement

*(include full problem description – makes the article self‑contained and keyword rich.)*

---

## 2. Why BFS?  

*(explain concept, include code snippets)*

---

## 3. Java Implementation

```java
// Java code block
```

---

## 4. Python Implementation

```python
# Python code block
```

---

## 5. C++ Implementation

```cpp
// C++ code block
```

---

## 6. Complexity Analysis

*(table, bullet points)*

---

## 7. Good, Bad & Ugly

*(section with sub‑headings and bullet points)*

---

## 8. Interview Tips

*(list of bullet points)*

---

## 9. Bonus: Check for Solvability

*(brief note on parity for larger puzzles)*

---

## 10. Takeaway

*(encourage practice & mention job relevance)*

```

> Publish this on Medium, Dev.to or your personal blog.  
> Add tags: `algorithms`, `interviews`, `leetcode`, `bfs`, `c++`, `java`, `python`.

--- 

### 🚀 Final Word

*The sliding puzzle is a **classic interview starter**.  
*By mastering BFS and writing clean, language‑agnostic code you prove you can:*
1. **Model a problem** → states & transitions.  
2. **Choose the right algorithm** → shortest‑path search.  
3. **Optimize** → small state space, constant‑time operations.  
4. **Communicate** → explain reasoning, pitfalls, and edge cases.

Show these skills in your next interview and watch recruiters notice!