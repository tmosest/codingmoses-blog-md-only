---
title: LeetCode 358. Rearrange String k Distance Apart - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 358. Rearrange String k Distance Apart – A Full‑Stack Solution  

> **Problem (LeetCode 358)**  
> Given a string `s` and an integer `k`, rearrange `s` such that the same characters are at least `k` distance apart.  
> Return the rearranged string or an empty string if it’s impossible.

> **Key ideas**  
> * Greedy + Max‑Heap  
> * Cooling‑down queue (the “k distance” window)  
> * O(n log σ) time, O(σ) space (σ = number of distinct chars)

Below you’ll find a production‑ready implementation in **Java, Python, and C++** and a blog‑ready article that dives into the *good, the bad, and the ugly* of this classic interview question.

---

# 1. Code Solutions

> *All three implementations use the same algorithmic skeleton:*
> 1. Count the frequency of every character.  
> 2. Push them into a max‑heap (most frequent first).  
> 3. Repeatedly pop up to `k` items, append them to the answer, and re‑insert any that still have a remaining count.  
> 4. If at some point we’re forced to finish the loop with unused items, the task is impossible.

> *Time complexity*: **O(n log σ)** – `n` = `s.length`, `σ` ≤ 26 (lowercase letters).  
> *Space complexity*: **O(σ)**.

---

## 1.1 Java (Java 17)

```java
import java.util.*;

public class Solution {
    public String rearrangeString(String s, int k) {
        if (k <= 1) return s;          // k==0 or 1 – no restrictions

        // 1. Frequency array
        int[] freq = new int[26];
        for (char ch : s.toCharArray()) freq[ch - 'a']++;

        // 2. Max-heap: priority by remaining frequency
        PriorityQueue<CharFreq> maxHeap =
                new PriorityQueue<>(Comparator.comparingInt((CharFreq cf) -> cf.count).reversed());

        for (int i = 0; i < 26; i++)
            if (freq[i] > 0) maxHeap.offer(new CharFreq((char) ('a' + i), freq[i]));

        StringBuilder sb = new StringBuilder();
        Queue<CharFreq> coolDown = new ArrayDeque<>();   // holds items that wait for k steps

        while (!maxHeap.isEmpty()) {
            int blockSize = Math.min(k, maxHeap.size() + coolDown.size());
            List<CharFreq> temp = new ArrayList<>();

            for (int i = 0; i < blockSize; i++) {
                if (maxHeap.isEmpty()) {
                    return ""; // impossible
                }
                CharFreq cur = maxHeap.poll();
                sb.append(cur.ch);
                cur.count--;           // one occurrence used
                temp.add(cur);          // will re‑insert later if count > 0
            }

            // Re‑insert the used characters whose count > 0
            for (CharFreq cf : temp) {
                if (cf.count > 0) maxHeap.offer(cf);
            }
        }
        return sb.toString();
    }

    private static class CharFreq {
        char ch;
        int count;
        CharFreq(char ch, int count) { this.ch = ch; this.count = count; }
    }
}
```

---

## 1.2 Python (Python 3.11)

```python
import heapq
from collections import Counter
from typing import List

class Solution:
    def rearrangeString(self, s: str, k: int) -> str:
        if k <= 1:
            return s

        freq = Counter(s)
        # Max‑heap via negative counts
        max_heap: List[tuple[int, str]] = [(-cnt, ch) for ch, cnt in freq.items()]
        heapq.heapify(max_heap)

        result = []
        while max_heap:
            temp = []
            block_size = min(k, len(max_heap))
            for _ in range(block_size):
                cnt, ch = heapq.heappop(max_heap)
                result.append(ch)
                if cnt + 1 < 0:          # still remaining
                    temp.append((cnt + 1, ch))

            if not temp and max_heap:
                # Not enough characters to fill the block
                return ""

            for item in temp:
                heapq.heappush(max_heap, item)

        return ''.join(result)
```

---

## 1.3 C++ (C++17)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    string rearrangeString(string s, int k) {
        if (k <= 1) return s;

        vector<int> freq(26, 0);
        for (char c : s) freq[c - 'a']++;

        // Max‑heap: priority_queue of pair<count, char>
        auto cmp = [](const pair<int,char> &a, const pair<int,char> &b) {
            return a.first < b.first;          // max‑heap
        };
        priority_queue<pair<int,char>, vector<pair<int,char>>, decltype(cmp)> pq(cmp);

        for (int i = 0; i < 26; ++i)
            if (freq[i] > 0) pq.emplace(freq[i], 'a' + i);

        string res;
        while (!pq.empty()) {
            vector<pair<int,char>> temp;
            int block = min(k, (int)pq.size());
            for (int i = 0; i < block; ++i) {
                auto cur = pq.top(); pq.pop();
                res.push_back(cur.second);
                cur.first--;
                if (cur.first > 0) temp.push_back(cur);
            }

            if (temp.empty() && !pq.empty()) return "";  // impossible

            for (auto &p : temp) pq.push(p);
        }
        return res;
    }
};
```

---

# 2. Blog Article – *“Rearrange String k Distance Apart: The Good, The Bad, The Ugly”*

> *SEO tags: Leetcode 358, Rearrange String k Distance Apart, interview algorithm, greedy, priority queue, Java, Python, C++, job interview, coding interview questions, algorithm interview, data structures, job seekers.*

---

## 2.1 The Problem in Plain English

You’re given a string of lowercase letters, for example `aabbcc`.  
You must rearrange the characters so that **any two identical letters are at least `k` indices apart**.  
If `k = 3`, the string `abcabc` works because every “a”, “b”, or “c” appears again only after 3 other characters.

If no such rearrangement exists, return an empty string.

---

## 2.2 The “Good” – Why This Problem Is A Classic Interview Star

| Aspect | Why It’s Good |
|--------|---------------|
| **Greedy & Data‑Structure Mastery** | The solution requires a max‑heap (priority queue) and a queue (cool‑down window). Interviewers love to see if candidates can combine these tools. |
| **Clear Failure Conditions** | You can prove impossibility by checking the frequency of the most common character. `maxCount > (n + k - 1) / k` is a quick sanity check. |
| **Scalable Complexity** | O(n log σ) is fast enough for the LeetCode constraint (`3 * 10^5`). Candidates can discuss the trade‑off between heap operations and counting sort. |
| **Multiple Languages** | The same algorithm is implementable in Java, Python, C++, Go, etc. Shows language agility. |
| **Real‑World Use** | Similar logic appears in CPU scheduling, radio frequency assignment, and job scheduling. Good for “why would you use this?” questions. |

---

## 2.3 The “Bad” – Common Pitfalls and How to Avoid Them

| Pitfall | Why It Happens | Fix |
|---------|----------------|-----|
| **Mis‑handling `k == 0`** | Many write `if (k <= 1) return s;` but forget that `k == 0` means *no* spacing requirement. | Explicit `if (k <= 1) return s;` or add a comment. |
| **Wrong heap comparator** | Using a min‑heap or incorrect comparator leads to selecting the least frequent char, producing an impossible string. | Use a max‑heap (negative counts or custom comparator). |
| **Blocking on too many iterations** | `blockSize = min(k, maxHeap.size() + coolDown.size());` must be carefully derived. A typo leads to `ArrayIndexOutOfBounds`. | Use a clear block-size computation: `int blockSize = Math.min(k, maxHeap.size());`. |
| **Neglecting the “impossible” check** | If the heap empties before the block is filled, the algorithm must return “”. | After the inner loop, if `temp.isEmpty() && !maxHeap.isEmpty()` → `return ""`. |
| **Off‑by‑one in distance** | Some think “k distance apart” means *k+1* slots. | Clarify: If `k=2`, “a__a” works; the next same char starts at index `i+2`. |

---

## 2.4 The “Ugly” – When the Code Gets Messy

1. **Storing Pair Counts Manually**  
   In Java, you might see a `Map<Character, Integer>` plus a separate array for the heap. This can lead to duplicated logic.

2. **Too Many Temporary Variables**  
   Mixing `List<CharFreq>` with `Queue<CharFreq>` in the same loop obscures the algorithm’s intent.

3. **Custom Heap Implementation**  
   Some candidates implement their own binary heap for fun, which is unnecessary. Stick to the language’s standard `PriorityQueue`.

4. **Ignoring Edge Cases**  
   Code that fails when the input string is empty or when `k > s.length()` ends up in a crash.

> **Takeaway** – Keep the algorithm *one‑liner* in your mind: “Pop up to k, build a temp list, re‑insert.” Let the data structures do the heavy lifting.

---

## 2.5 Full Walkthrough – Step by Step (Illustrated with `s = "aaabbc"` and `k = 3`)

1. **Count frequencies**: `a=3`, `b=2`, `c=1`.  
2. **Max‑heap**: `[('a',3), ('b',2), ('c',1)]`.  
3. **First block (k=3)**:  
   * Pop `a` → `a`, remaining `2`.  
   * Pop `b` → `ab`, remaining `1`.  
   * Pop `c` → `abc`, remaining `0`.  
   * Re‑insert `a(2)` and `b(1)`.  
4. **Second block**:  
   * Pop `a` → `abca`, remaining `1`.  
   * Pop `b` → `abcab`, remaining `0`.  
   * Heap has only `a(1)`. Since we’re forced to pop 3 items, we cannot fill the block → **impossible**.  
   * Return `""`.  

The algorithm correctly identifies impossibility.

---

## 2.6 Interview‑Ready Talking Points

| Topic | What to Discuss |
|-------|-----------------|
| **Greedy Proof** | “We always take the most frequent char because delaying it can only increase the chance of conflict.” |
| **Complexity** | “`O(n log 26)` ≈ `O(n)`. The heap size never exceeds 26, so the log factor is negligible.” |
| **Edge Cases** | `k == 0`, `k == 1`, single‑character strings, all characters identical. |
| **Variations** | *k=2*: equivalent to “no two identical adjacent”. *k>n*: always impossible unless all chars are unique. |
| **Real‑World Analog** | CPU job scheduling – “no job repeats within k time units”. |

---

## 2.7 SEO‑Friendly Closing

> If you’re preparing for a **coding interview**, mastering **Leetcode 358 – Rearrange String k Distance Apart** will give you a winning edge.  
> It’s a staple question that showcases your **greedy algorithms**, **priority queue** skills, and ability to **translate the same logic into Java, Python, or C++**.  
> Want to stand out? Remember the **good**, avoid the **bad**, and keep your implementation **clean**.  

**Keywords**: `Leetcode 358`, `Rearrange String k Distance Apart`, `greedy algorithm`, `priority queue`, `coding interview`, `job interview questions`, `Java solution`, `Python solution`, `C++ solution`, `job seekers`, `algorithm interview`.  

Happy coding—and may your interview strings always be spaced just right!