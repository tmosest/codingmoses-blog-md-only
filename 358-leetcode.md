---
title: LeetCode 358. Rearrange String k Distance Apart - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📄 Blog Title  
**Rearrange String `k` Distance Apart – 358 | Hard | LeetCode**  
*The Good, The Bad, and The Ugly – How One Problem Can Land You Your Dream Job*

---

### 1️⃣ Why This Problem Matters

- **Interview Power‑Move** – A perfect “show‑case” of greedy + heap + queue.
- **LeetCode Rank** – 358, Hard, 620 k+ votes → *“Must‑Solve”* in many hiring pipelines.
- **Real‑World Parallel** – Scheduling tasks with cooling periods, CPU task scheduler, etc.
- **Language Flexibility** – Solutions exist in Java, Python, C++ – the language of your stack.

---

## 🔍 Problem Statement (LeetCode 358)

> **Input**  
> `String s` (lowercase letters only)  
> `int k` (0 ≤ k ≤ |s|)
> 
> **Output**  
> Return a *rearranged* string such that the same characters are at least distance `k` apart.  
> If no such arrangement exists, return the empty string `""`.

**Examples**

| s          | k | Output     | Reason                            |
|------------|---|------------|-----------------------------------|
| `aabbcc`   | 3 | `abcabc`   | Each `a`, `b`, `c` 3 steps apart |
| `aaabc`    | 3 | `""`       | Impossible                        |
| `aaadbbcc` | 2 | `abacabcd` | Each same char ≥ 2 steps apart    |

---

## 📌 Core Challenges

| Challenge | Why It’s Hard |
|-----------|---------------|
| **Feasibility Check** | Determining if the arrangement is possible before spending time. |
| **Balancing Frequencies** | The most frequent char may block the whole string. |
| **Avoiding Adjacent Duplicates** | Classic greedy problem – pick the best next candidate. |
| **Large Constraints** | |s| can be 3 × 10⁵ → O(n log σ) must be fast. |
| **Multiple Languages** | Need idiomatic implementations for Java, Python, C++. |

---

## 🧠 Algorithm (Greedy + Priority Queue + Cool‑down Queue)

1. **Count Frequencies** – `Map<char, int> freq`.
2. **Max‑Heap** – Store pairs `(count, char)` sorted by *count* descending.
3. **Cooldown Queue** – Keep characters that are “waiting” until `k` steps are passed. Each entry stores `(char, remainingCount, readyIndex)`.
4. **Build Result**  
   * While heap not empty:  
     1. Pop the most frequent character.  
     2. Append to result.  
     3. Decrease its count.  
     4. If it still has remaining occurrences, push it into the cooldown queue with `readyIndex = currentIndex + k`.  
     5. If the front of the cooldown queue is ready (`readyIndex == currentIndex`), push it back into the heap.  
5. **Validate** – If the built string’s length < |s| → impossible → return `""`.

This is essentially the same idea used in the “Task Scheduler” problem on LeetCode.

---

## ⚙️ Implementation – Java

```java
import java.util.*;

public class Solution {
    public String rearrangeString(String s, int k) {
        if (k == 0) return s;                 // trivial case

        // 1. frequency count
        int[] freq = new int[26];
        for (char c : s.toCharArray()) freq[c - 'a']++;

        // 2. max‑heap (PriorityQueue with custom comparator)
        PriorityQueue<int[]> pq = new PriorityQueue<>(
            (a, b) -> b[0] - a[0]); // a[0] = count, a[1] = char index

        for (int i = 0; i < 26; i++)
            if (freq[i] > 0) pq.offer(new int[]{freq[i], i});

        // 3. cooldown queue
        Queue<int[]> waitQueue = new LinkedList<>(); // {charIdx, count, readyIdx}

        StringBuilder sb = new StringBuilder();
        int pos = 0; // current position in result

        while (!pq.isEmpty() || !waitQueue.isEmpty()) {
            // release from cooldown if ready
            if (!waitQueue.isEmpty() && waitQueue.peek()[2] == pos) {
                int[] ready = waitQueue.poll();
                pq.offer(new int[]{ready[1], ready[0]});
            }

            if (pq.isEmpty()) {
                // cannot place remaining chars
                return "";
            }

            int[] curr = pq.poll();
            sb.append((char) ('a' + curr[1]));
            curr[0]--; // used one occurrence

            if (curr[0] > 0) {
                // will be available after k steps
                waitQueue.offer(new int[]{curr[1], curr[0], pos + k});
            }

            pos++;
        }

        return sb.toString();
    }
}
```

**Key Points**

- `int[] {count, idx}` keeps the heap simple.
- Cool‑down queue uses `readyIdx` – the exact position when the character may be re‑pushed.
- Complexity: `O(n log 26) ≈ O(n)`.

---

## ⚙️ Implementation – Python

```python
import heapq
from collections import Counter, deque

class Solution:
    def rearrangeString(self, s: str, k: int) -> str:
        if k == 0:
            return s

        freq = Counter(s)
        # Max‑heap (invert counts)
        heap = [(-cnt, ch) for ch, cnt in freq.items()]
        heapq.heapify(heap)

        wait = deque()   # (char, remaining_count, ready_at)
        res = []
        pos = 0

        while heap or wait:
            # Release cooled‑down chars
            if wait and wait[0][2] == pos:
                ch, cnt, _ = wait.popleft()
                heapq.heappush(heap, (-cnt, ch))

            if not heap:
                return ""  # cannot place

            cnt, ch = heapq.heappop(heap)
            res.append(ch)
            cnt += 1  # since cnt is negative
            if cnt < 0:
                wait.append((ch, -cnt, pos + k))

            pos += 1

        return "".join(res)
```

**Python‑specific Tips**

- `heapq` is a min‑heap, so use negative counts for max‑heap behaviour.
- `deque` gives O(1) pops from the left for cooldown.

---

## ⚙️ Implementation – C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string rearrangeString(string s, int k) {
        if (k == 0) return s;

        unordered_map<char,int> freq;
        for (char c : s) ++freq[c];

        // Max‑heap: pair<count, char>
        auto cmp = [](const pair<int,char>& a, const pair<int,char>& b){
            return a.first < b.first;           // descending count
        };
        priority_queue<pair<int,char>, vector<pair<int,char>>, decltype(cmp)> pq(cmp);

        for (auto &p : freq) pq.emplace(p.second, p.first);

        // cooldown queue: {char, remaining, ready_at}
        queue<tuple<char,int,int>> wait;
        string res;
        int pos = 0;

        while (!pq.empty() || !wait.empty()) {
            // Release cooled‑down chars
            if (!wait.empty() && get<2>(wait.front()) == pos) {
                auto [ch, cnt, _] = wait.front(); wait.pop();
                pq.emplace(cnt, ch);
            }

            if (pq.empty()) return "";          // impossible

            auto [cnt, ch] = pq.top(); pq.pop();
            res.push_back(ch);
            if (--cnt > 0)                       // still left
                wait.emplace(ch, cnt, pos + k);

            ++pos;
        }

        return res;
    }
};
```

**C++‑specific Notes**

- Use `unordered_map` for frequency counting.
- `priority_queue` already behaves as a max‑heap for pairs.
- `queue<tuple<>>` for the cooldown, exactly the same logic as Java/Python.

---

## 🔍 Complexity Analysis (Same for All Languages)

| Operation | Time | Reason |
|-----------|------|--------|
| Count frequency | `O(n)` | linear scan |
| Build heap | `O(σ)` | σ ≤ 26 |
| Main loop | `O(n log σ)` | each pop/push on the heap (σ is tiny) |
| Cool‑down queue | `O(n)` | each char inserted & removed once |
| **Total** | **`O(n)`** | n ≤ 300 000 → well within 1 s limits |

Space: `O(σ + n)` – the heap, frequency table, and the output string.

---

## 🛑 Common Pitfalls (“The Ugly”)

| Pitfall | Fix |
|---------|-----|
| **Forgetting k = 0** | Immediate return – otherwise the loop can dead‑lock. |
| **Using 0‑based vs 1‑based indices** | Keep `ready_at = pos + k`. If you use 1‑based, adjust accordingly. |
| **Not releasing cooldown** | Leads to an impossible‑string return even when a solution exists. |
| **Ignoring the feasibility test** | For very large `k`, you might still succeed; no early “return” needed – algorithm handles it. |
| **Wrong heap ordering** | In Python, using a min‑heap requires negating counts; in C++ use `greater` or custom comparator. |

---

## 🎯 Interview‑Ready Checklist

1. **Explain the Greedy Idea** – “Take the most frequent char that’s not cooling.”
2. **Show the Cool‑down Concept** – Tie into “cooling period” or “waiting room”.
3. **Write Clean Code** – Use built‑in collections; comment where necessary.
4. **Edge‑Case Testing** – Run `k = 0`, `k = |s|`, single‑letter strings, all same letters.
5. **Complexity Talk** – Emphasise `O(n log σ)`; highlight that σ (alphabet size) is constant.

> *Pro tip*: “Why did you choose a heap? What would happen if you just sorted each time?” – this shows you understand data‑structure trade‑offs.

---

## 📈 Real‑World Significance

- **Task Scheduler** – CPU jobs with cooling intervals.
- **CPU Resource Management** – Prevent cache thrashing.
- **Game Server Tick Scheduling** – Ensuring events are not too close.
- **Manufacturing Assembly Lines** – Maintain distance between identical items.

Showing that you can solve this problem proves you can *model* a problem, *choose* the right data‑structure, and *implement* a high‑performance solution.

---

## 🎯 How This Solves *Your* Interview Problem

1. **Shows Mastery of Greedy** – Most Hard problems hinge on this.
2. **Demonstrates Heap + Queue** – Employers look for familiarity with STL/Java Collections.
3. **Highlights Performance Mindset** – O(n) solution for a 300k input → “I can handle large data.”
4. **Portability** – Implemented in Java, Python, C++ – whatever the company uses.
5. **Discussion Starter** – “Can we guarantee feasibility?” – invites deeper discussion about optimality proofs.

> **Bottom line:** Pull this solution into your portfolio (GitHub, LeetCode profile) and in your interview, be ready to walk through the *feasibility*, *heap*, *cool‑down*, and *time‑complexity* discussions. Recruiters will see you can think in terms of *tasks + cooling*, a classic interview staple.

---

## 🚀 Next Steps for Your Career

| Action | What It Shows |
|--------|---------------|
| **Add to GitHub** – `leetcode-358-rearrange.cpp`, `rearrange.py`, `Solution.java`. | Code‑clean, comments, tests. |
| **Write a Blog Post** – Like this one. | Demonstrates communication skills. |
| **Practice on LQ* | Add random tests; try edge cases. | |
| **Mock Interviews** – Pair with a friend or use Interviewing.io. | Get feedback on explanation. |
| **Reach Out on LinkedIn** – “Solved LeetCode 358 – see my post for details.” | Show initiative. |

---

### 🎉 Takeaway

Rearranging a string with a cooling period is *not* just a puzzle; it’s a microcosm of real‑world scheduling, a classic greedy problem, and a must‑know interview technique. Master it in Java, Python, and C++, understand the underlying heap + cooldown queue mechanics, and you’ll have a concrete talking point that proves you’re ready for any high‑level software engineering role.

Good luck, and may your interview output be *always* valid and never empty! 🚀

---

> **SEO Keywords**  
> `Rearrange String k Distance Apart`, `LeetCode 358`, `job interview coding`, `Java solution`, `Python solution`, `C++ solution`, `priority queue`, `cooldown queue`, `greedy algorithm`, `task scheduler`

---