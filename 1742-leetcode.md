---
title: LeetCode 1742. Maximum Number of Balls in a Box - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 Mastering LeetCode 1742 – “Maximum Number of Balls in a Box”

> **SEO Keywords**: LeetCode 1742, Maximum Number of Balls in a Box, coding interview, Java solution, Python solution, C++ solution, algorithm interview, job interview, data structures, array, hash map, time complexity

---

## TL;DR

- **Problem**: For every integer *i* in `[lowLimit, highLimit]` put ball *i* into box `sumDigits(i)`.  
- **Goal**: Return the largest number of balls in any box.  
- **Constraints**: `1 ≤ lowLimit ≤ highLimit ≤ 10⁵`  
- **Best Solution**: O(n) time, O(1) space (array of size 46).  
- **Why It Matters**: A frequent interview question that tests your ability to translate a simple problem into an efficient algorithm.  

---

## 1️⃣ Problem Breakdown

| Step | What Happens | Why It Matters |
|------|--------------|----------------|
| 1 | **Balls** are numbered from `lowLimit` to `highLimit`. | Defines the input range. |
| 2 | **Boxes** are numbered by the sum of digits of the ball’s number. | Core transformation – `box = sumDigits(ball)`. |
| 3 | Count how many balls land in each box. | We need the maximum count. |
| 4 | Return that maximum. | Final answer. |

> **Example**  
> `lowLimit = 1`, `highLimit = 10`  
> Ball 1 → box 1, Ball 2 → box 2, … Ball 10 → box 1  
> Box 1 gets 2 balls → answer `2`.

---

## 2️⃣ The Good

| Feature | Explanation | Why It’s Good |
|---------|-------------|---------------|
| **Simple Loop** | Iterate over each number once. | Linear time, easy to reason about. |
| **Direct Digit Sum** | While `num > 0`, add `num % 10` and divide by 10. | No recursion or string conversion – constant overhead. |
| **Array of Size 46** | The maximum digit sum for numbers up to 100 000 is `9 × 5 = 45`. | O(1) space; eliminates hashmap overhead. |
| **Single Pass Max Update** | Track `maxCount` while filling array. | One pass over data + one pass over fixed-size array → still O(n). |

---

## 3️⃣ The Bad

| Problem | Common Pitfall | Fix |
|---------|----------------|-----|
| **HashMap Overhead** | Using a `Map<Integer, Integer>` adds allocation and hashing cost. | Replace with fixed-size array. |
| **String Conversion** | `String.valueOf(i).chars().sum()` is slower than numeric digit extraction. | Use arithmetic operations. |
| **Incorrect Sum Bound** | Using an array size of 100 000 (or a large constant) wastes memory. | Size should be `45 + 1`. |
| **Off‑by‑One Errors** | Mis‑counting boxes because of inclusive vs exclusive ranges. | Verify loop boundaries (`i <= highLimit`). |

---

## 4️⃣ The Ugly

| Ugly Aspect | Why It’s Ugly | Remedy |
|-------------|---------------|--------|
| **Unnecessary Recursion** | Recursively summing digits can hit stack limits and is harder to debug. | Use iterative approach. |
| **Over‑engineering** | Implementing a sophisticated DP or combinatorics solution when a simple loop suffices. | Stick to the most direct algorithm. |
| **Hard‑coded Limits** | Hard‑coding `45` as max sum without explanation makes maintenance risky. | Compute `maxSumDigits(highLimit)` dynamically or document the derivation. |
| **Poor Variable Naming** | Using vague names like `arr`, `cnt`, `boxNo` obscures intent. | Use expressive names: `digitSum`, `boxCount`. |

---

## 5️⃣ The Code

Below are clean, idiomatic solutions in **Java**, **Python**, and **C++** that follow the *Good* approach described above.

### 5.1 Java

```java
import java.util.Arrays;

public class Solution {
    public int countBalls(int lowLimit, int highLimit) {
        // Max digit sum for numbers up to 100000 is 45
        int[] boxCount = new int[46];
        int maxBalls = 0;

        for (int i = lowLimit; i <= highLimit; i++) {
            int box = digitSum(i);
            boxCount[box]++;
            if (boxCount[box] > maxBalls) {
                maxBalls = boxCount[box];
            }
        }
        return maxBalls;
    }

    private int digitSum(int n) {
        int sum = 0;
        while (n > 0) {
            sum += n % 10;
            n /= 10;
        }
        return sum;
    }
}
```

#### Why This Is Fast

- **Time**: `O(n)` – one loop over the range, constant‑time digit sum.
- **Space**: `O(1)` – array of 46 integers regardless of input size.

### 5.2 Python

```python
class Solution:
    def countBalls(self, lowLimit: int, highLimit: int) -> int:
        # 9 * 5 = 45 is the max possible sum of digits for 100000
        box_count = [0] * 46
        max_balls = 0

        for num in range(lowLimit, highLimit + 1):
            box = self._digit_sum(num)
            box_count[box] += 1
            if box_count[box] > max_balls:
                max_balls = box_count[box]
        return max_balls

    @staticmethod
    def _digit_sum(n: int) -> int:
        s = 0
        while n:
            s += n % 10
            n //= 10
        return s
```

### 5.3 C++

```cpp
class Solution {
public:
    int countBalls(int lowLimit, int highLimit) {
        // 9 * 5 = 45 for numbers up to 100000
        vector<int> boxCount(46, 0);
        int maxBalls = 0;

        for (int num = lowLimit; num <= highLimit; ++num) {
            int box = digitSum(num);
            ++boxCount[box];
            if (boxCount[box] > maxBalls) {
                maxBalls = boxCount[box];
            }
        }
        return maxBalls;
    }

private:
    int digitSum(int n) {
        int sum = 0;
        while (n) {
            sum += n % 10;
            n /= 10;
        }
        return sum;
    }
};
```

> **Tip**: In C++ you could also use `std::array<int, 46> boxCount{}` for static allocation.

---

## 6️⃣ Time & Space Complexity

| Language | Time | Space |
|----------|------|-------|
| Java | **O(n)** where `n = highLimit - lowLimit + 1` | **O(1)** (array of 46 ints) |
| Python | **O(n)** | **O(1)** |
| C++ | **O(n)** | **O(1)** |

> Even with the worst-case `n = 100 000`, the solution runs in a few milliseconds.

---

## 7️⃣ Interview‑Ready Takeaways

1. **Read the constraints** – 10⁵ is small enough for a simple loop.
2. **Avoid over‑engineering** – a hashmap is unnecessary here.
3. **Know the upper bound of digit sums** – helps you pick the array size.
4. **Talk through your logic** – explain why an array of size 46 is enough.
5. **Mention edge cases** – e.g., when `lowLimit == highLimit`, or when all numbers share the same digit sum.

---

## 8️⃣ Bonus: A Brute‑Force vs. Optimized Comparison

| Approach | Complexity | Pros | Cons |
|----------|------------|------|------|
| **Brute‑Force (HashMap)** | `O(n log m)` (log m from digit extraction) | Easy to write | Extra memory, slower due to hashing |
| **Optimized (Array of 46)** | `O(n)` | Minimal memory, fastest | Requires knowledge of digit‑sum bound |

> If your interviewer insists on using a map, at least explain the trade‑off.

---

## 9️⃣ Wrap‑Up & Next Steps

✅ **You now have a clean, production‑ready implementation** in your favorite language.  
✅ **You understand the “Good‑Bad‑Ugly” spectrum** – great for explaining trade‑offs on the fly.  
✅ **You can answer the “why” question confidently** – a key skill in coding interviews.

---

### Want more interview prep?

- **Subscribe** to our weekly newsletter for deeper dives into LeetCode problems.  
- **Download** the free “30‑Day Interview Prep” e‑book (link in bio).  
- **Ask** us a question on Stack Overflow or GitHub Discussions – we’re always happy to help!

Good luck in your interviews! 🎯

---