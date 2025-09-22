---
title: LeetCode 3191. Minimum Operations to Make Binary Array Elements Equal to One I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1. Problem Recap  
**LeetCode 3191 – Minimum Operations to Make Binary Array Elements Equal to One**  
- Input: a binary array `nums` (each element is 0 or 1)  
- Operation: pick any **3 consecutive** elements and flip all of them (`0 → 1`, `1 → 0`)  
- Goal: transform every element to `1` using the minimum number of operations, or return `-1` if impossible.

**Why this problem matters**  
* The greedy strategy required here is a classic interview trick that shows you understand “once you’ve fixed an index, you never need to touch it again.”  
* It tests your ability to think in linear time, handle edge‑cases, and write clean code in multiple languages.

Below you’ll find three production‑ready solutions—**Java, Python, and C++**—and a short blog post that explains the logic, highlights the pitfalls, and gives you interview‑ready talking points.

---

## 2. Solution Outline – Simple Greedy

### 2.1  Key Observation  
When you reach index `i`, you can decide immediately whether a flip is needed:

* If `nums[i]` is `1`, keep it.  
* If `nums[i]` is `0`, the only way to make it `1` is to flip the window `[i, i+1, i+2]`.  
  * After this flip, indices `i`, `i+1`, `i+2` are guaranteed to have the correct parity for the future (they’ll never be looked at again).

Thus a **single left‑to‑right pass** is enough.  
After the pass, the last two indices must already be `1`.  
If either of them is `0`, no sequence of operations can fix the array, so we return `-1`.

### 2.2  Complexity  
* **Time** – `O(n)` – one scan of the array.  
* **Space** – `O(1)` – we only use a counter and a few loop variables.

---

## 3. Code – Java

```java
import java.util.*;

public class Solution {
    public int minOperations(int[] nums) {
        int n = nums.length;
        int ops = 0;

        // iterate only until n-3, because we flip in groups of 3
        for (int i = 0; i <= n - 3; i++) {
            if (nums[i] == 0) {
                // flip the three consecutive elements
                nums[i] ^= 1;
                nums[i + 1] ^= 1;
                nums[i + 2] ^= 1;
                ops++;
            }
        }

        // after the loop the last two positions must be 1
        return (nums[n - 2] == 1 && nums[n - 1] == 1) ? ops : -1;
    }

    // Simple test harness
    public static void main(String[] args) {
        Solution s = new Solution();
        System.out.println(s.minOperations(new int[]{0,1,1,1,0,0})); // 3
        System.out.println(s.minOperations(new int[]{0,1,1,1}));      // -1
    }
}
```

**Why it passes:**  
* The `^= 1` toggles the bit in place – no extra array.  
* We never touch indices that are already processed, guaranteeing optimality.

---

## 4. Code – Python

```python
from typing import List

class Solution:
    def minOperations(self, nums: List[int]) -> int:
        n = len(nums)
        ops = 0

        for i in range(n - 2):
            if nums[i] == 0:
                # flip 3 consecutive elements
                nums[i] ^= 1
                nums[i + 1] ^= 1
                nums[i + 2] ^= 1
                ops += 1

        # ensure the last two elements are 1
        return ops if nums[-2] == 1 and nums[-1] == 1 else -1

# Demo
if __name__ == "__main__":
    s = Solution()
    print(s.minOperations([0,1,1,1,0,0]))  # 3
    print(s.minOperations([0,1,1,1]))      # -1
```

*Python’s* `^= 1` operator is concise and fast; the same greedy logic applies.

---

## 5. Code – C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int minOperations(vector<int>& nums) {
        int n = nums.size(), ops = 0;
        for (int i = 0; i <= n - 3; ++i) {
            if (nums[i] == 0) {
                // flip the window [i, i+1, i+2]
                for (int j = 0; j < 3; ++j) nums[i + j] ^= 1;
                ++ops;
            }
        }
        return (nums[n-2] == 1 && nums[n-1] == 1) ? ops : -1;
    }
};

int main() {
    Solution s;
    cout << s.minOperations({0,1,1,1,0,0}) << '\n'; // 3
    cout << s.minOperations({0,1,1,1}) << '\n';     // -1
}
```

---

## 6. What Makes This “Good”  

| ✔ | Reason |
|---|--------|
| **Linear time** | Only one pass (`O(n)`) – essential for large arrays up to `10^5`. |
| **Constant space** | No auxiliary data structures – keeps memory usage minimal. |
| **Deterministic** | The greedy rule is provably optimal – no backtracking needed. |
| **Language‑agnostic** | Same logic applies to Java, Python, C++ – perfect for interview demos. |
| **Clean & readable** | Simple loop, few lines of code – reduces bugs. |

---

## 7. Common Pitfalls (the “Bad”)  

| ❌ | Pitfall | Fix |
|---|----------|-----|
| **Going past the array end** | Accessing `i+2` when `i` is `n-2` or `n-1`. | Loop to `i <= n-3` (or `i < n-2` in Python). |
| **Using a non‑linear algorithm** | Trying BFS or DP can hit `O(2^n)` or high memory. | Stick to the greedy sweep. |
| **Ignoring the last two elements** | Failing to check `nums[n-2]` and `nums[n-1]`. | Return `-1` if they’re not both `1`. |
| **In‑place modification vs copy** | Some interviewers may expect you to preserve the input. | Clarify whether mutation is allowed; if not, work on a copy. |

---

## 8. Why You’ll Shine in an Interview (the “Ugly” but powerful part)  

1. **Explain the invariant**  
   *“After processing index i, we guarantee that `nums[i]` is 1 and will never be changed again.”*  
   Demonstrates deep understanding of why the greedy strategy works.

2. **Edge‑case analysis**  
   *“If the last two elements aren’t 1 after the sweep, we can prove no sequence of operations will fix them.”*  
   Shows you think about impossible cases, not just the happy path.

3. **Complexity talk**  
   *“The algorithm runs in O(n) time and O(1) extra space, which meets the constraints for n up to 10⁵.”*  
   Confirms you can translate constraints into algorithmic decisions.

4. **Language trade‑offs**  
   *“In Java we use `^= 1` for an in‑place toggle; in Python it’s the same; in C++ a small inner loop does the job.”*  
   Signals that you’re comfortable writing idiomatic code across languages.

5. **Optional discussion**  
   *“If mutation weren’t allowed, we could still use a boolean array or a bitset to keep track of flips.”*  
   Shows you can adapt to different interview constraints.

---

## 9. Take‑away Cheat Sheet  

| Step | What to say | Code snippet |
|------|-------------|--------------|
| **1. Scan left to right** | “Flip whenever you hit a 0.” | `for i in range(n-2): if nums[i]==0: …` |
| **2. Flip window** | “Toggle the next three bits.” | `nums[i] ^= 1; nums[i+1] ^= 1; nums[i+2] ^= 1` |
| **3. Count ops** | “Increment a counter for each flip.” | `ops += 1` |
| **3. Validate tail** | “Both `n‑2` and `n‑1` must be 1.” | `return ops if nums[-2]==1 and nums[-1]==1 else -1` |

Keep this map handy while you answer “Can you do it faster?” or “What if the array is very long?”

---

## 10. Wrap‑Up & Interview‑Ready Questions  

* **Could a different window size work?** – Explain why 3 is required; any other size would break the invariant.  
* **What if the array length is less than 3?** – Immediate return `-1` unless it’s already all 1s.  
* **Can we prove optimality?** – Sketch a short proof: each flip changes exactly one 0 at position `i` and never creates a new 0 at earlier indices.  

Answering these will show you’re *not* just coding, you’re *architecting* a solution.

---

## 10. SEO‑Friendly Summary  

> **LeetCode 3191 solution** – *Java, Python, C++ greedy* – linear time, constant space – interview strategy – binary array flipping – impossible‑case detection – optimal solution – talk through invariants and edge cases.

Add this post to your LinkedIn, GitHub README, or portfolio. Search engines will pick up the keywords above, and recruiters looking for “LeetCode 3191” or “greedy binary array problems” will find you instantly.

Happy coding—and good luck on your next interview!