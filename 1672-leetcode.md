---
title: LeetCode 1672. Richest Customer Wealth - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # The “Richest Customer Wealth” Problem (LeetCode 1672) – A Full‑Stack Guide  
**(Java | Python | C++)**  

> *Want to ace the algorithm part of your next software‑engineering interview?  
>  Read on for a deep‑dive into LeetCode 1672 – “Richest Customer Wealth”.  
>  The article covers the problem statement, a clean O(m·n) solution, edge‑case pitfalls, and a quick‑reference implementation in Java, Python, and C++.  
>  All code is ready to copy‑paste into your IDE, plus a short test harness so you can run it locally.*

---

## 1. Problem Recap

> **LeetCode 1672 – Richest Customer Wealth**  
> You are given a 2‑D integer array `accounts` of size `m × n`.  
> `accounts[i][j]` denotes the amount of money the *i‑th* customer has in the *j‑th* bank.  
> The **wealth** of a customer is the sum of all their bank balances.  
> Return the **maximum** wealth among all customers.

**Constraints**

| Parameter | Value |
|-----------|-------|
| `m = accounts.length` | `1 ≤ m ≤ 50` |
| `n = accounts[i].length` | `1 ≤ n ≤ 50` |
| `1 ≤ accounts[i][j] ≤ 100` | |

**Examples**

| Input | Output | Explanation |
|-------|--------|-------------|
| `[[1,2,3],[3,2,1]]` | `6` | Both customers have wealth 6. |
| `[[1,5],[7,3],[3,5]]` | `10` | Second customer has 7 + 3 = 10. |
| `[[2,8,7],[7,1,3],[1,9,5]]` | `17` | Third customer has 1 + 9 + 5 = 15, but the first customer has 2 + 8 + 7 = 17. |

---

## 2. “The Good” – A Straight‑Forward O(m × n) Solution

The simplest way to find the richest customer is:

1. **Iterate** over each customer (row).  
2. **Sum** the balances in that row.  
3. **Track** the maximum sum encountered.

Because every element is visited exactly once, the algorithm runs in **O(m × n)** time and uses **O(1)** extra space.

### Why This is Good

| ✅ | Reason |
|---|--------|
| **Clarity** | No auxiliary data structures or advanced tricks – just two loops. |
| **Performance** | Meets the problem’s constraints effortlessly (≤ 2500 operations). |
| **Maintainability** | Easy to read and modify for interview or production code. |
| **Language‑agnostic** | Works in Java, Python, C++, Go, etc. |

---

## 3. “The Bad” – Things That Can Go Wrong

| ❌ | Pitfall |
|---|---------|
| **Off‑by‑one errors** | Forgetting to include the last bank or mis‑indexing `accounts[i][j]`. |
| **Integer overflow** | In languages with limited integer ranges (e.g., Java’s `int` can hold up to 2 147 483 647). With max values 50 × 100 = 5000, overflow isn’t an issue here, but beware for larger constraints. |
| **Unnecessary copying** | Using `Collections.max()` on a stream or building an intermediate list of sums adds overhead and complicates the code. |
| **Null/empty inputs** | Not guarding against `null` or empty arrays can crash the program in some environments. |
| **Ignoring performance on large inputs** | If the problem size grows (e.g., 10⁵ × 10⁵), the O(m × n) solution would be unacceptable. |

---

## 4. “The Ugly” – Over‑Optimized or Over‑Complicated Variants

| 💩 | Ugly Approach |
|---|----------------|
| **Bit‑wise tricks** | Using `|=` or `&=` on sums is nonsense; bitwise operators are for binary manipulation, not arithmetic sums. |
| **Recursive row summation** | A naive recursive helper that sums rows can blow up the call stack and adds overhead. |
| **Vectorized NumPy (Python)** | While broadcasting might look slick, it hides the O(m × n) cost and adds a heavy dependency. |
| **Thread‑pool parallelism** | Splitting the rows across threads can actually slow down the algorithm for small inputs (due to thread‑creation overhead). |
| **Custom data structures** | Building a binary heap or segment tree to track max sums is overkill for a single pass problem. |

> **Bottom line:** Keep it simple. Complexity does not always equal cleverness.

---

## 5. Code Walkthrough – Three Languages

Below are **ready‑to‑run** snippets for each language, each featuring:

* A single `public int maximumWealth(int[][] accounts)` (Java) / `def maximum_wealth(accounts)` (Python) / `int maximumWealth(vector<vector<int>>& accounts)` (C++) method.  
* A **main** or **test harness** that runs the three example cases and prints the output.

---

### 5.1 Java

```java
// RichestCustomerWealth.java
import java.util.*;

public class RichestCustomerWealth {

    /**
     * Returns the maximum wealth among all customers.
     * @param accounts 2D array where accounts[i][j] is the balance of customer i at bank j
     * @return maximum wealth
     */
    public int maximumWealth(int[][] accounts) {
        int maxWealth = 0;                 // start with 0 (since all balances > 0)
        for (int[] customer : accounts) {  // iterate rows
            int current = 0;
            for (int balance : customer) { // iterate columns
                current += balance;
            }
            if (current > maxWealth) {
                maxWealth = current;
            }
        }
        return maxWealth;
    }

    // ---- Simple test harness ----
    public static void main(String[] args) {
        RichestCustomerWealth solver = new RichestCustomerWealth();

        int[][][] tests = {
            {{1,2,3},{3,2,1}},
            {{1,5},{7,3},{3,5}},
            {{2,8,7},{7,1,3},{1,9,5}}
        };

        for (int i = 0; i < tests.length; i++) {
            System.out.printf("Test %d: %d%n", i+1,
                solver.maximumWealth(tests[i]));
        }
    }
}
```

Compile & run:

```bash
javac RichestCustomerWealth.java
java RichestCustomerWealth
```

Output:

```
Test 1: 6
Test 2: 10
Test 3: 17
```

---

### 5.2 Python

```python
# richest_customer_wealth.py

def maximum_wealth(accounts):
    """
    Compute the richest customer's wealth.

    :param accounts: List[List[int]] – customers × banks
    :return: int – maximum wealth
    """
    max_wealth = 0
    for customer in accounts:
        current = sum(customer)      # Python's built‑in sum is efficient
        if current > max_wealth:
            max_wealth = current
    return max_wealth

# ---- Simple test harness ----
if __name__ == "__main__":
    tests = [
        [[1, 2, 3], [3, 2, 1]],
        [[1, 5], [7, 3], [3, 5]],
        [[2, 8, 7], [7, 1, 3], [1, 9, 5]]
    ]

    for i, test in enumerate(tests, 1):
        print(f"Test {i}: {maximum_wealth(test)}")
```

Run:

```bash
python3 richest_customer_wealth.py
```

Output:

```
Test 1: 6
Test 2: 10
Test 3: 17
```

---

### 5.3 C++

```cpp
// RichestCustomerWealth.cpp
#include <iostream>
#include <vector>
#include <numeric> // for std::accumulate

int maximumWealth(const std::vector<std::vector<int>>& accounts) {
    int maxWealth = 0;
    for (const auto& customer : accounts) {
        int current = std::accumulate(customer.begin(), customer.end(), 0);
        if (current > maxWealth) {
            maxWealth = current;
        }
    }
    return maxWealth;
}

int main() {
    std::vector<std::vector<int>> tests[] = {
        {{1, 2, 3}, {3, 2, 1}},
        {{1, 5}, {7, 3}, {3, 5}},
        {{2, 8, 7}, {7, 1, 3}, {1, 9, 5}}
    };

    for (int i = 0; i < 3; ++i) {
        std::cout << "Test " << i+1 << ": " << maximumWealth(tests[i]) << std::endl;
    }

    return 0;
}
```

Compile & run:

```bash
g++ -std=c++17 -O2 -pipe -static -s -o richest RichestCustomerWealth.cpp
./richest
```

Output:

```
Test 1: 6
Test 2: 10
Test 3: 17
```

---

## 6. Time / Space Complexity

| Complexity | Explanation |
|------------|-------------|
| **O(m × n)** | Every element of the `accounts` matrix is visited once to compute row sums. |
| **O(1)** | Only a few integer variables are used; no extra data structures. |

With the given constraints (`m, n ≤ 50`), the algorithm will finish in microseconds.

---

## 7. Common Interview Follow‑Ups

| Question | Suggested Answer |
|----------|------------------|
| *Can you explain why O(m × n) is optimal?* | The problem requires inspecting each balance at least once to compute a customer’s total. Therefore, you cannot do better than linear in the input size. |
| *What if `m` and `n` were 10⁵ each?* | The input would be 10¹⁰ integers—impossible to store in memory. In that scenario, you’d need streaming or external‑memory algorithms, but the LeetCode constraints don’t require it. |
| *How would you parallelize this?* | You could split the rows among multiple threads, each computing a local maximum, then combine them. However, for the small input sizes on LeetCode, the overhead outweighs benefits. |
| *What if negative balances were allowed?* | The algorithm remains the same, but you might want to handle the case where all sums are negative. In that case, initializing `maxWealth` to `INT_MIN` (Java) or `-∞` (Python) would be safer. |

---

## 8. Wrap‑Up – What to Keep In Your Interview Toolbox

1. **Simplicity** – Your first solution should be the cleanest one.  
2. **Clarity** – Explain each step to the interviewer.  
3. **Edge Cases** – Mention overflow and empty inputs even if they’re not tested.  
4. **Ready‑to‑Use Code** – Keep the snippets from this post handy for a quick copy‑paste during practice.  

---

## 8. Bonus – “What If” Variation

> **Problem Twist:** Find the *least* wealthy customer.  
> **Solution:** Same code, but track `minWealth` instead of `maxWealth`.

---

## 9. TL;DR

* A **single pass** over the `accounts` matrix, summing each row and tracking the maximum, is the cleanest, fastest, and most interview‑friendly solution.  
* Avoid over‑complicated tricks or unnecessary dependencies; the problem is solved in **O(m × n)** time and **O(1)** space.  

Happy coding and good luck with LeetCode #1672 (maximum‑wealth)! 🎉

---

## 10. Further Reading

1. **LeetCode Discussion Thread** – https://leetcode.com/problems/richest-customer-wealth/discuss  
2. **Java Streams vs Loops** – https://stackoverflow.com/a/44130210  
3. **C++ std::accumulate** – https://en.cppreference.com/w/cpp/algorithm/accumulate  
4. **Python sum vs Loop** – https://docs.python.org/3/library/functions.html#sum  

---

### 11. SEO & Visibility Checklist

| ✅ | SEO Element |
|---|-------------|
| **Target Keyword** | “Richest customer wealth” |
| **Sub‑Keywords** | “maximum wealth”, “O(m × n) solution”, “LeetCode 1672” |
| **Meta Title** | “Richest Customer Wealth – O(m × n) Solution (Java, Python, C++)” |
| **Meta Description** | “Learn the best O(m × n) approach to LeetCode 1672. Clear Java, Python, C++ code, complexity, and interview tips.” |
| **Headers** | H1 – Problem Title; H2 – The Good, The Bad, The Ugly; H3 – Code Walkthrough; etc. |
| **Canonical URL** | https://yourblog.com/richest-customer-wealth |
| **Image ALT Text** | “Illustration of algorithm steps for richest customer wealth.” |
| **Internal Links** | Link to other LeetCode problem solutions on your site. |

---

> **Remember**: In an interview, your explanation is as valuable as the code. Show confidence, ask clarifying questions, and keep your solution simple yet correct. Good luck! 🚀

---


---

> **Posted by**: Jane Doe – Software Engineer, Interview Coach, Algorithm Enthusiast  
> **Date**: 2023‑09‑28  

---