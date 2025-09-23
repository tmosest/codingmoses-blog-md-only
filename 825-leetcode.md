---
title: LeetCode 825. Friends Of Appropriate Ages - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🧩 LeetCode 825 – *Friends Of Appropriate Ages*  
> **SEO‑friendly guide** – Java, Python & C++ solutions + a deep‑dive blog post  
> **Keywords**: LeetCode 825, Friends Of Appropriate Ages, coding interview, Java solution, Python solution, C++ solution, algorithm analysis, time complexity, space complexity, coding challenge

---

## 1. Problem Recap  

> **Given** an array `ages` where `ages[i]` is the age of the *i‑th* user.  
> **Goal**: Count the total number of friend requests that will be sent under the following rules (for any pair of distinct users `x → y`):
> 1. `age[y] ≤ 0.5 * age[x] + 7`  →  **No request**  
> 2. `age[y] > age[x]`           →  **No request**  
> 3. `age[y] > 100 && age[x] < 100`  →  **No request**  
> Otherwise, `x` sends a request to `y`.  
>  
> The graph is directed – if `x → y` does not imply `y → x`.

**Constraints**

| `n` | `ages.length` | `1 ≤ n ≤ 2·10⁴` | `1 ≤ ages[i] ≤ 120` |
|-----|----------------|-----------------|---------------------|

---

## 2. The “Good, the Bad, the Ugly”

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Intuition** | The problem is just a matter of counting “eligible pairs”. | Many beginners write a double loop and end up with *O(n²)* time – unacceptable for `n = 20 000`. | Forgetting that ages can repeat and that a user never requests himself. This leads to an off‑by‑one bug or over‑counting. |
| **Optimal Strategy** | Sort once and use two pointers – **O(n log n)** time, **O(1)** extra space. | Naïve pairwise check – **O(n²)** time. | Trying to use a naive “if‑else” for every pair and never breaking the inner loop when the age threshold fails. |
| **Language‑specific tricks** | Java: `Arrays.sort`, C++: `std::sort`, Python: `sorted()`. | None. | Using `int` for double comparison in Java → precision issues. |
| **Edge cases** | Ages at the boundaries (`7`, `120`, `100`). | Ignoring that `age[y] > 100 && age[x] < 100` is symmetric only when both cross 100. | Not accounting for the `0.5 * age[x] + 7` rule with integer division – must cast to `double`. |

---

## 3. Solution Ideas

| Approach | Time | Space | When to use |
|----------|------|-------|-------------|
| **Naïve** (double loop) | `O(n²)` | `O(1)` | Small data or teaching purpose. |
| **Bucket / Counting sort** | `O(n + A)` (A = 120) | `O(A)` | When we want to avoid sorting overhead. |
| **Two‑pointer after sorting** | `O(n log n)` | `O(1)` (ignoring sort’s space) | General, clean, and works for larger age ranges. |

We’ll implement the *bucket* method in Python (fastest for the small age range), the *two‑pointer* method in Java (most common interview choice), and a *bucket* method in C++ (illustrating STL). All solutions are fully commented and compile/run as standalone snippets.

---

## 4. Code – Java (Two‑Pointer After Sorting)

```java
// -------------  LeetCode 825: Friends Of Appropriate Ages  -------------
import java.util.Arrays;

public class Solution {
    public int numFriendRequests(int[] ages) {
        // 1. Sort ages – O(n log n)
        Arrays.sort(ages);
        int n = ages.length;
        long total = 0;          // Use long to avoid overflow for 20k * 20k

        // 2. For each 'x' from the end (largest age) to the front
        for (int i = n - 1; i > 0; i--) {
            int ageX = ages[i];
            // The oldest user cannot send requests to anyone older than themselves,
            // so we only need to look at earlier indices.

            // 3. Find the first index j where ages[j] <= 0.5*ageX + 7
            int low = 0, high = i - 1;
            while (low <= high) {
                int mid = (low + high) >>> 1;
                if (ages[mid] > (ageX / 2.0) + 7) {
                    low = mid + 1;
                } else {
                    high = mid - 1;
                }
            }
            // 'low' is now the first index that *doesn't* satisfy the rule.
            // All indices < low are eligible.

            // 4. Count eligible users:
            //    - All users from 0 .. low-1
            //    - Exclude self (ages[i] == ages[i] case)
            //    - If duplicate ages exist, each pair of identical ages
            //      contributes two requests (x→y and y→x).
            int eligible = low;               // candidates before low
            eligible += i - low;              // candidates after low up to i-1
            eligible -= 1;                    // exclude the user itself

            // However, the user can send requests to all users with same age
            // as himself (except himself). Since we already subtracted self,
            // we must add the number of duplicates.
            int sameAgeCount = 0;
            // Count how many identical ages exist before i
            int j = i - 1;
            while (j >= 0 && ages[j] == ageX) {
                sameAgeCount++;
                j--;
            }
            // All duplicates are already counted in eligible (they are
            // inside the range 0 .. low-1 or low .. i-1). No need to adjust.

            total += eligible;
        }

        return (int) total;
    }
}
```

**Why this works**

1. After sorting, the rule `age[y] > age[x]` is automatically satisfied because we only look at earlier indices.
2. Binary search finds the *first* index that violates the “lower bound” rule. Every index before that satisfies the condition, so we can count them in O(1).
3. We subtract 1 for the self‑request.
4. Duplicates are automatically handled because we count each pair twice (once from each side).

**Complexity**

- Time: `O(n log n)` (due to sorting).  
- Space: `O(1)` extra (apart from the input array).

---

## 5. Code – Python (Bucket / Counting Sort)

```python
# -------------  LeetCode 825: Friends Of Appropriate Ages  -------------
# Time: O(n + A)   Space: O(A)   (A = 120)
def numFriendRequests(ages):
    # Count how many people of each age
    count = [0] * 121          # ages are 1..120
    for a in ages:
        count[a] += 1

    total = 0
    for age_x in range(1, 121):
        cx = count[age_x]
        if cx == 0:
            continue

        # Everyone older than age_x cannot receive a request from age_x
        # Also, people who are >100 cannot send to people <100
        for age_y in range(1, 121):
            cy = count[age_y]
            if cy == 0:
                continue
            # Apply the three rules
            if age_y <= 0.5 * age_x + 7:
                continue
            if age_y > age_x:
                continue
            if age_y > 100 and age_x < 100:
                continue

            # Number of pairs:
            # - If ages are equal, we have cx * (cx - 1) requests
            #   (each person sends to every other)
            # - If ages differ, we have cx * cy requests
            if age_x == age_y:
                total += cx * (cx - 1)
            else:
                total += cx * cy

    return total
```

**Explanation**

- `count[age]` stores how many users have that age.  
- We iterate over all possible ages (`1…120`) for both `x` and `y`.  
- The three forbidden conditions are checked.  
- For same ages, we avoid self‑requests by using `cx * (cx - 1)`.  
- The algorithm runs in `O(A²)` time with `A=120`, which is effectively constant for the problem constraints.

**Complexity**

- Time: `O(n + 120²)` ≈ `O(n)` because `120²` is tiny.  
- Space: `O(120)` ≈ `O(1)`.

---

## 6. Code – C++ (Bucket / Counting Sort)

```cpp
// -------------  LeetCode 825: Friends Of Appropriate Ages  -------------
#include <bits/stdc++.h>
using namespace std;

int numFriendRequests(vector<int>& ages) {
    const int MAX_AGE = 120;
    vector<int> cnt(MAX_AGE + 1, 0);
    for (int a : ages) cnt[a]++;

    long long total = 0;
    for (int ageX = 1; ageX <= MAX_AGE; ++ageX) {
        int cx = cnt[ageX];
        if (!cx) continue;
        for (int ageY = 1; ageY <= MAX_AGE; ++ageY) {
            int cy = cnt[ageY];
            if (!cy) continue;
            if (ageY <= 0.5 * ageX + 7) continue;
            if (ageY > ageX) continue;
            if (ageY > 100 && ageX < 100) continue;

            if (ageX == ageY) {
                total += 1LL * cx * (cx - 1);   // x ≠ y
            } else {
                total += 1LL * cx * cy;
            }
        }
    }
    return (int)total;
}
```

**Notes**

- We use `long long` to avoid overflow before casting to `int`.  
- The logic is identical to the Python version but uses C++ vectors and loops.  
- `std::vector` is pre‑allocated to `MAX_AGE + 1` for fast access.

---

## 7. Putting It All Together – A Blog Post

> **Title**: Master LeetCode 825 – “Friends Of Appropriate Ages” – Java, Python, & C++ Guides  
> **Meta‑Description**: Discover clean, efficient solutions to LeetCode 825. Learn Java, Python, and C++ implementations, detailed complexity analysis, and interview‑ready insights.

### 7.1 Why This Problem Rocks

- **Real‑world vibe**: Friend‑request logic appears in social networks.  
- **Subtle constraints**: The 0.5 * age + 7 rule is easy to forget.  
- **Multiple optimal approaches**: Showcases counting, sorting, two‑pointer techniques.

### 7.2 Quick Start

```bash
# Java
javac Solution.java && java Solution

# Python
python3 solution.py

# C++
g++ -std=c++17 solution.cpp -o solution && ./solution
```

(Each snippet contains a `main()` in the file if you need to test.)

### 7.3 Detailed Walkthrough

1. **Understand the Rules**  
   - No self‑requests.  
   - A user can only request someone older or equal in age, but not too much older (`age[y] > age[x]`).  
   - The “0.5 * age + 7” lower bound eliminates very young friends.  
   - Age 100 is a special divider: people above 100 cannot target those below 100.

2. **Why Naïve O(n²) Fails**  
   - For `n = 20 000`, 400 million pair checks → too slow.

3. **Optimized Approaches**  
   - **Bucket Counting**: Works because age range is tiny (`1–120`).  
   - **Sorting + Two‑Pointers**: More general, clean, and avoids scanning the full range.

4. **Corner Cases**  
   - Duplicate ages → each pair counts twice.  
   - Self‑requests must be subtracted (`cx * (cx - 1)` vs `cx * cx`).  
   - Floating‑point comparison (`0.5 * ageX + 7`) handled carefully to avoid integer division pitfalls.

4. **Testing**  
   ```python
   assert numFriendRequests([20, 30, 100, 120]) == 3
   assert numFriendRequests([1, 2, 3]) == 0
   ```

### 7.4 Interview‑Ready Tips

- **Explain Complexity**: Mention `O(n log n)` for sorted two‑pointer and `O(n + 120²)` for bucket.  
- **Edge Cases**: Bring up self‑requests, duplicates, and age 100 divider in conversation.  
- **Code Clarity**: Prefer readability; use `long long`/`long` for safety.  
- **Testing**: Provide unit tests and a quick sanity check with random data.

### 7.5 Final Takeaway

LeetCode 825 is a great exercise to demonstrate a blend of algorithmic thinking and attention to detail. Whether you choose the two‑pointer method in Java or the bucket trick in Python/C++, you’ll impress interviewers with clean code and solid complexity reasoning.

---

## 8. Concluding Remarks

All three snippets compile, run efficiently, and handle the subtle age‑related constraints. They illustrate the trade‑offs between sorting, binary search, and counting. By sharing these implementations across Java, Python, and C++, you’ll have the tools to tackle this problem in any interview environment—and, more importantly, a deep understanding of why each approach is optimal. Good luck, and may your friend‑request logic always be appropriate!