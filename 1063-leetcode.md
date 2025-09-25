---
title: LeetCode 1063. Number of Valid Subarrays - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1063 – Number of Valid Subarrays  
**Hard** – LeetCode

> **Goal**  
> For an array `nums`, count every contiguous sub‑array whose *leftmost* element is **not larger** than any other element in that sub‑array.  
> (In other words, the leftmost element is the minimum of that sub‑array.)

The constraints (`n ≤ 5·10⁴`) force an O(n) solution – a classic case for a *monotonic stack*.

Below you’ll find a clean, production‑ready implementation in **Java, Python, and C++**.  
After the code, a fully‑SEO‑optimised blog post explains the algorithm, the pitfalls, and why it will impress recruiters.

---

## 📚 Code – Three Languages

> All three snippets solve the same problem in **O(n)** time and **O(n)** auxiliary space.  
> They all follow the exact same logic: maintain an increasing stack of indices; when a smaller number appears, pop and compute contributions.

### 1. Java

```java
import java.util.*;

public class Solution {
    /**
     * Counts the number of valid subarrays.
     *
     * @param nums the input array
     * @return the count of valid subarrays
     */
    public int validSubarrays(int[] nums) {
        int n = nums.length;
        Deque<Integer> stack = new ArrayDeque<>();   // stores indices in increasing order
        long total = 0;                              // use long to avoid overflow

        for (int i = 0; i < n; i++) {
            // While the current element is smaller, the top of the stack
            // cannot extend past i-1.
            while (!stack.isEmpty() && nums[i] < nums[stack.peek()]) {
                int idx = stack.pop();
                total += i - idx;                    // subarrays where nums[idx] is leftmost
            }
            stack.push(i);
        }

        // Remaining indices can extend to the end of the array
        while (!stack.isEmpty()) {
            int idx = stack.pop();
            total += n - idx;
        }

        return (int) total;  // result fits in int (≤ n*(n+1)/2)
    }
}
```

---

### 2. Python

```python
from typing import List

class Solution:
    def validSubarrays(self, nums: List[int]) -> int:
        stack: List[int] = []          # indices of elements in increasing order
        total: int = 0
        n: int = len(nums)

        for i, val in enumerate(nums):
            # pop all indices whose values are larger than the current value
            while stack and nums[stack[-1]] > val:
                idx = stack.pop()
                total += i - idx       # number of subarrays with nums[idx] as leftmost
            stack.append(i)

        # Remaining indices reach the end of the array
        while stack:
            idx = stack.pop()
            total += n - idx

        return total
```

---

### 3. C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int validSubarrays(vector<int>& nums) {
        int n = nums.size();
        vector<int> st;           // stack of indices
        long long total = 0;      // use long long to be safe

        for (int i = 0; i < n; ++i) {
            while (!st.empty() && nums[st.back()] > nums[i]) {
                int idx = st.back(); st.pop_back();
                total += i - idx;          // contribution of nums[idx]
            }
            st.push_back(i);
        }

        // Remaining indices can extend to the end
        while (!st.empty()) {
            int idx = st.back(); st.pop_back();
            total += n - idx;
        }

        return static_cast<int>(total);
    }
};
```

> **Why `long long`/`long`?**  
> For `n = 5·10⁴`, the maximum number of subarrays is `n*(n+1)/2 ≈ 1.25·10⁹`, which fits into a 32‑bit signed int. However, intermediate additions can exceed that bound in a naïve implementation. Using a 64‑bit accumulator guarantees safety.

---

## 📝 Blog Post – “The Good, The Bad, and The Ugly of LeetCode 1063”

> **Target Audience** – CS students, software‑engineering interviewees, and recruiters  
> **Keywords** – *Number of Valid Subarrays*, *LeetCode 1063*, *monotonic stack*, *software‑engineering interview*, *algorithm interview*, *job interview algorithm*, *software engineer*

---

### Title  
**“Number of Valid Subarrays (LeetCode 1063): The Good, The Bad, and The Ugly – A Monotonic‑Stack Guide for Job‑Ready Coders”**

---

#### 1. Introduction – Why This Problem Matters

- **LeetCode Hard** – appears in many hiring pipelines (Google, Amazon, Microsoft).
- **Real‑world analogy** – finding “weakest link” in a chain of constraints (e.g., the earliest time a job can start given deadlines).
- **Interview signal** – tests your grasp of *subarray counting*, *stack tricks*, and *amortized analysis*.

---

#### 2. Problem Recap

Given `nums[0…n-1]`, count the number of non‑empty contiguous sub‑arrays where the **leftmost** element is the smallest in that sub‑array.  
Formally: for a sub‑array `nums[l…r]`, we require `nums[l] ≤ nums[i]` for all `l < i ≤ r`.

> **Key Insight** – The leftmost element is the **minimum** of the sub‑array.

---

#### 3. The Good – Elegant O(n) Solution

- **Monotonic Increasing Stack**  
  *Store indices of increasing values.*  
  When a smaller element appears, all indices on the stack are “frozen” – they can no longer extend past the current position.

- **Contribution Counting**  
  For an index `idx` popped at position `i`, the number of valid sub‑arrays where `nums[idx]` is the leftmost element equals `i - idx`.  
  *Why?* All sub‑arrays `[idx, k]` with `k` ranging from `idx` to `i-1` have `nums[idx]` as their minimum (because no smaller element was seen yet).

- **Second Pass**  
  Remaining indices after the main loop can extend to the end of the array, contributing `n - idx` sub‑arrays each.

**Pseudo‑code (Java‑style)**

```java
for (i = 0; i < n; i++) {
    while (!stack.isEmpty() && nums[i] < nums[stack.peek()]) {
        idx = stack.pop();
        total += i - idx;
    }
    stack.push(i);
}
while (!stack.isEmpty()) {
    idx = stack.pop();
    total += n - idx;
}
```

**Complexity** – Each index is pushed and popped at most once → **O(n)** time, **O(n)** space.

> **Why it’s the “Good”** –  
> *Simplicity*: One linear pass, one stack, clear mathematical rationale.  
> *Robustness*: Handles equal values, strictly increasing, strictly decreasing, and random data without special cases.

---

#### 4. The Bad – Common Pitfalls

| Pitfall | Why it breaks | Fix |
|---------|----------------|-----|
| **Using a stack of values instead of indices** | You lose the distance information (`i - idx`). | Store indices; look up values via `nums[idx]`. |
| **Using a decreasing stack** | You’ll end up counting *rightmost* minima, not leftmost. | Use an increasing stack (`nums[stack[top]] < nums[i]`). |
| **Counting after each pop with `i - idx`** | Fails when duplicate values appear; the condition `nums[i] < nums[stack[top]]` must be *strictly* smaller. | Keep `>` for duplicates or handle duplicates in the second pass. |
| **Not handling the remaining stack after the loop** | Misses sub‑arrays that extend to the array end. | Final loop: `total += n - idx`. |
| **Using int overflow** | For large arrays, intermediate sums exceed 32‑bit. | Use `long`/`long long` for the accumulator. |

> **Why it’s “Bad”** – These bugs are subtle, but they make a solution fail fast on hidden tests. They’re also easy to spot if you think carefully about the invariants.

---

#### 5. The Ugly – Overkill & Inefficient Solutions

1. **Brute Force** – Triple nested loops → **O(n³)**, impossible for `n = 50k`.
2. **Prefix‑Min + Binary Search** – Still **O(n²)** in worst case.
3. **Segment Tree / RMQ** – Over‑engineered: building an O(n) tree and querying O(log n) for each sub‑array → **O(n² log n)**.
4. **Two‑Pointer Sliding Window** – Works for *sum* or *product* constraints but not for “minimum” based constraints; you'd need a deque to maintain mins → still **O(n²)** if not careful.

> The “ugly” solutions are often the first ones that candidates think of, but they’re *not* competitive. An interview panel will expect a clean O(n) stack solution. Demonstrating awareness of these pitfalls shows depth.

---

#### 6. Real‑World Takeaway

- **Monotonic Stacks** appear in many contexts: next‑greater‑element, sliding window minima, convex hull maintenance, etc.  
- Master the pattern → you’ll recognize it instantly during interviews.  
- Always think in terms of *“when does an element stop being relevant?”* – that’s the core of amortized O(n) stack tricks.

---

#### 7. Sample Interview Dialogue

> **Interviewer:** “Why O(n) is enough? Could you explain the amortized analysis in plain English?”  
> **Candidate:**  
> “Each element can be pushed onto the stack once. After it’s popped, it’s never seen again. Thus the total number of push/pop operations is bounded by `2n`, giving linear time.”  

> This crisp answer showcases both correctness and performance understanding.

---

#### 7.1 Checklist for Your Submission

- [ ] Store **indices**, not values.  
- [ ] Use **strictly** smaller comparison for popping.  
- [ ] Count `i - idx` on pop.  
- [ ] Process remaining stack after loop.  
- [ ] Accumulate with `long`/`long long`.  
- [ ] Write clean, well‑commented code.  

> Submit the Java snippet, but bring the Python or C++ version on the day – many hiring managers run your solution in multiple runtimes.

---

#### 7. Bonus – Visualizing the Stack Dynamics

```
Index:   0  1  2  3  4  5
Value:   3  2  1  1  4  2

Stack evolution (top = leftmost):

i=0: stack=[0]
i=1: pop 0 (3>2) → contrib 1, stack=[1]
i=2: pop 1 (2>1) → contrib 1, stack=[2]
i=3: no pop (1==1), stack=[3,2]
i=4: no pop (4>1), stack=[4,3,2]
i=5: pop 4 (4>2) → contrib 1, pop 3 (1>2? no), pop 2 (1>2? no), stack=[5,2]
Final: pop 5→ 1, pop 2→ 4
Total = 1+1+1+1+4 = 8
```

> Seeing the stack visually helps solidify the reasoning and is great for a white‑board interview.

---

#### 8. Closing – Impress Recruiters

- **Talk about amortized O(n)** – recruiters love “why this is fast”.
- **Show the second pass** – many candidates forget it; it demonstrates attention to detail.
- **Explain overflow handling** – indicates production‑level code mindset.
- **Mention the pattern** – “This is a classic monotonic stack problem, which also underpins next‑greater‑element, sliding‑window minima, etc.”  
  They’ll instantly see that you can transfer this knowledge to other problems.

---

### 🚀 Final Thought

LeetCode 1063 is more than just a counting problem – it’s a gateway to mastering monotonic stack techniques that dominate technical interviews.  
With the code above and the deep dive in this article, you’re fully equipped to convert “the bad and ugly” into a clean, **job‑ready** solution that will stand out to recruiters.

Happy coding, and good luck on the next interview!

--- 

*Feel free to copy, adapt, and share.*