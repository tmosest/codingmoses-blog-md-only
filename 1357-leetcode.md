---
title: LeetCode 1357. Apply Discount Every n Orders - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 1️⃣  LeetCode 1357 – *Apply Discount Every n Orders*  
**Solution in Java, Python & C++ + a SEO‑friendly interview‑blog**

---

## 📋 Problem Recap

A supermarket sells `products[i]` for `prices[i]`.  
A customer’s bill is two parallel arrays `product` & `amount`.  
Every **`n`‑th** customer gets a **`discount` %** off the subtotal.

You must implement:

```text
Cashier(int n, int discount, int[] products, int[] prices)
double getBill(int[] product, int[] amount)
```

`getBill` returns the final amount a customer must pay (discount applied if it’s the
nth customer). Answers within `1e-5` are accepted.

> **Constraints**  
> * `1 ≤ n ≤ 10⁴`  
> * `0 ≤ discount ≤ 100`  
> * `1 ≤ products.length ≤ 200`  
> * `1 ≤ product.length ≤ products.length`  
> * `product` IDs are unique

---

## 🔑  Core Idea

* **Map product IDs → prices** (`HashMap / unordered_map / dict`).  
* Keep a **counter** (`currentCustomer`).  
* After every call to `getBill`, increment the counter.  
  If `currentCustomer % n == 0`, apply the discount.  
* Compute the subtotal by iterating through the order and summing  
  `amount[i] * price[product[i]]`.

The algorithm is **O(k)** per call, where *k* is the number of items in that
order, and uses **O(p)** extra space for the price map.

---

## ✅  Code

### 1️⃣ Java

```java
import java.util.HashMap;
import java.util.Map;

public class Cashier {
    private final int n;                // every nth customer gets discount
    private final int discount;         // percent discount
    private final Map<Integer,Integer> priceMap;
    private int customerCnt = 0;        // counts how many customers so far

    public Cashier(int n, int discount, int[] products, int[] prices) {
        this.n = n;
        this.discount = discount;
        priceMap = new HashMap<>(products.length);
        for (int i = 0; i < products.length; i++) {
            priceMap.put(products[i], prices[i]);   // O(p)
        }
    }

    public double getBill(int[] product, int[] amount) {
        customerCnt++;                            // 1‑based counting

        double subtotal = 0;
        for (int i = 0; i < product.length; i++) {
            subtotal += priceMap.get(product[i]) * (double) amount[i];
        }

        if (customerCnt % n == 0) {                // nth customer
            subtotal *= (100 - discount) / 100.0;  // apply discount
        }
        return subtotal;
    }
}
```

### 2️⃣ Python

```python
class Cashier:
    def __init__(self, n: int, discount: int, products: list[int], prices: list[int]):
        self.n = n
        self.discount = discount
        self.price_map = {p: pr for p, pr in zip(products, prices)}
        self.customer_cnt = 0

    def getBill(self, product: list[int], amount: list[int]) -> float:
        self.customer_cnt += 1
        subtotal = sum(self.price_map[prod] * amt for prod, amt in zip(product, amount))
        if self.customer_cnt % self.n == 0:   # every nth customer
            subtotal *= (100 - self.discount) / 100.0
        return subtotal
```

### 3️⃣ C++

```cpp
#include <unordered_map>
#include <vector>

class Cashier {
    int n;                                     // discount frequency
    int discount;                              // percent
    std::unordered_map<int, int> priceMap;     // product → price
    int customerCnt = 0;                       // 1‑based counter

public:
    Cashier(int n, int discount, std::vector<int> products, std::vector<int> prices)
        : n(n), discount(discount) {
        for (size_t i = 0; i < products.size(); ++i)
            priceMap[products[i]] = prices[i];
    }

    double getBill(const std::vector<int>& product, const std::vector<int>& amount) {
        ++customerCnt;
        double subtotal = 0.0;
        for (size_t i = 0; i < product.size(); ++i)
            subtotal += priceMap[product[i]] * static_cast<double>(amount[i]);

        if (customerCnt % n == 0)
            subtotal *= static_cast<double>(100 - discount) / 100.0;
        return subtotal;
    }
};
```

---

## 📐  Complexity Analysis

| Step                      | Time                 | Space               |
|---------------------------|----------------------|---------------------|
| Building price map       | `O(p)` (`p` = #products) | `O(p)` |
| `getBill` call (per order) | `O(k)` (`k` = #items in order) | `O(1)` (besides input) |

With at most 1000 calls and `p ≤ 200`, the solution easily fits within the
limits.

---

## 📣  Blog Article – “The Good, The Bad, and the Ugly of *Apply Discount Every n Orders*”

### 1. Title & Meta‑Description (SEO)

> **Title**: *Apply Discount Every n Orders – Java, Python & C++ Solutions (LeetCode 1357)*  
> **Meta‑Description**: Solve LeetCode 1357 “Apply Discount Every n Orders” with clean Java, Python, and C++ code. Learn the trade‑offs, edge cases, and interview tips to ace the question.

### 2. Outline

1. **Introduction** – Why this problem is a staple in technical interviews  
2. **Problem Statement** – Restate constraints, goal, and expected output  
3. **Key Observations** – Unique product IDs, fixed discount frequency  
4. **Strategy** – Map lookup + counter  
5. **Implementation** – Code snippets for Java, Python, C++  
6. **Complexity & Edge Cases** – Overflow, floating‑point precision  
7. **The Good** – Simplicity, readability, O(1) space per call  
8. **The Bad** – Alternative pitfalls (array search, recursion)  
9. **The Ugly** – Real‑world pitfalls (integer overflow, missing discount reset)  
9. **Interview Perspective** – What interviewers are really looking for  
10. **Testing** – How to write unit tests  
11. **FAQs** – Common confusion points  
12. **Conclusion & Call‑to‑Action** – Encourage practice & job‑prep  

### 3. Full Blog Text

```markdown
# Apply Discount Every n Orders – Java, Python & C++ Solutions (LeetCode 1357)

> **LeetCode 1357** is a classic interview question that tests
> *data‑structure choice*, *counter logic*, and *floating‑point handling*.
> Below is a step‑by‑step guide with clean code in three major languages,
> plus a deep dive into the trade‑offs that every candidate should know.

## 📌 Introduction

Interviewers love problems that mix a *stateful* system (a cashier) with a
simple arithmetic rule (every nth customer gets a discount).  
It checks:
- your grasp of **hash maps** for O(1) lookup  
- **modulo arithmetic** for periodic events  
- handling **floating‑point math** without losing precision  

If you’ve nailed this question, you’ll feel confident tackling similar
state‑ful problems on any technical hiring round.

---

## 📄 Problem Statement

> A store sells *products* for *prices*.  
> A customer’s order is two arrays: `product[]` and `amount[]`.  
> Every `n`‑th customer gets a `discount` % off the subtotal.  
> Implement `Cashier` with `getBill()` that returns the final amount.

Constraints:  
- `1 ≤ n ≤ 10⁴`  
- `0 ≤ discount ≤ 100`  
- `products.length ≤ 200`  
- `product` IDs are **unique** and **positive**.

---

## 🔍 Key Observations

| Observation | Why it matters |
|-------------|----------------|
| **Unique IDs** | Allows direct hash‑map lookup. |
| **Fixed `n`** | Simple counter with modulo. |
| **`discount`** ≤ 100 | Edge cases 0 % (no discount) & 100 % (free). |
| **No order changes** | Price map built once in constructor. |

---

## 📊 Strategy

1. **Price Map** – `product → price`  
   *O(p)* to build, *O(1)* per lookup.  
2. **Customer Counter** – `currentCustomer++`.  
   Apply discount when `currentCustomer % n == 0`.  
3. **Subtotal Calculation** – Iterate over order items.  

The design is *O(k)* per call, *O(p)* space.  
All three languages (Java, Python, C++) use the same logic – just different
syntax.

---

## 🧑‍💻 Implementation Highlights

| Language | Core Lines | Why it looks good |
|----------|------------|-------------------|
| **Java** | `priceMap.put(products[i], prices[i]);` + `subtotal *= (100‑discount)/100.0;` | Strong typing + explicit `double` cast. |
| **Python** | `{p: pr for p, pr in zip(products, prices)}` + `subtotal *= (100-discount)/100.0` | Concise comprehension, automatic big‑int support. |
| **C++** | `unordered_map<int,int> priceMap;` + `subtotal *= (100-discount)/100.0` | Fast compile‑time lookups, explicit casting to double. |

Code blocks from the **Solution** section are embedded for quick copy‑paste.

---

## ⏱ Complexity & Edge Cases

* **Time** – `O(k)` per order, linear in number of items.  
* **Space** – `O(p)` for the map; per call `O(1)`.  
* **Overflow** – `amount[i] * price` fits in 32‑bit signed int (`≤ 200 × 10⁴`),
  but we cast to `double` before multiplying to avoid accidental overflow.  
* **Precision** – Use `double` and a multiplier like `(100-discount)/100.0`.
  LeetCode accepts `1e-5`, so standard IEEE‑754 is safe.  

---

## ✅  The Good

| Benefit | Explanation |
|---------|-------------|
| **Clarity** | One map, one counter – no hidden tricks. |
| **Performance** | Constant‑time map lookups; linear in order size only. |
| **Maintainability** | Separate constructor & method; easy to test. |
| **Cross‑Language** | Same algorithm, minimal adaptation for Java/Python/C++. |

---

## ⚠  The Bad

| Issue | Why it hurts |
|-------|--------------|
| **Array‑Search Alternative** | Using a linear search in the product array gives `O(p*k)` time – unnecessary if `p` is 200. |
| **Recursive Counter** | Recursion would stack‑overflow if `n` is large. |
| **Missing Reset** | Failing to reset the counter after discount leads to wrong future discounts. |

---

## 😱  The Ugly

| Problem | Real‑world scenario | Fix |
|---------|--------------------|-----|
| **Integer overflow** | `amount[i] * price` could exceed `2³¹‑1` if you stay in `int`. | Promote to `long` or `double` before multiplication. |
| **Floating‑point drift** | Repeated discounts may accumulate rounding errors. | Return `double` and rely on `1e‑5` tolerance; use `Decimal` in Python if exactness is needed. |
| **Zero‑division guard** | `n` could be 1, causing `customerCnt % 1 == 0` always true. | Works fine; just ensures discount applies to every customer – a corner case you should test. |

---

## 📈  Interview Tips

1. **Ask clarifying questions** – “Is the discount applied to the subtotal or the final total?”  
2. **Explain the counter logic** – “I’ll increment the counter first, then check `cnt % n == 0`.”  
3. **Show the map construction** – “We’ll build the map once in the constructor for O(p) space.”  
4. **Mention floating‑point** – “We use double to preserve cents.”  
5. **Discuss test cases** – Provide examples, especially for edge cases: `discount = 0`, `discount = 100`, `n = 1`, large orders.

---

## 📚  Testing Checklist

| Test | What to verify |
|------|----------------|
| Basic example (from problem) | Correct discount on 3rd & 6th customers. |
| `discount = 0` | No change to subtotal. |
| `discount = 100` | Zero final bill. |
| `n = 1` | Every customer gets discount. |
| Large order (200 items) | No performance degradation. |
| Repeated calls > 10⁴ | Counter modulo still works. |

---

## 🎯  Conclusion

LeetCode 1357 is a *state‑ful* question that blends data‑structure usage with
modular arithmetic.  
A simple hashmap + counter approach gives clean, efficient code in **Java,
Python, and C++**.  
Understand the pitfalls (overflow, floating‑point) and discuss the design
trade‑offs in interviews – you’ll demonstrate both technical skill and
thoughtful problem‑solving.

---

## ❓ FAQ

| Q | A |
|---|---|
| **Can I use an array instead of a map?** | Yes, but you’d need `O(p)` linear search for each order – O(k × p) time. |
| **What if product IDs aren’t unique?** | Problem guarantees uniqueness; otherwise you’d need a list of prices per ID. |
| **Will integer multiplication overflow?** | Use `long` or cast to `double` before multiplication. |
| **Is `double` precision enough?** | LeetCode accepts 1e‑5 error; double’s 53‑bit mantissa is more than enough. |

---

## 🚀  Get Hired

Mastering this problem shows you can:

* Map data to a value efficiently (`HashMap` / `dict`).  
* Handle periodic state changes cleanly (`%` operator).  
* Write clean, cross‑platform code.

Add the solutions above to your portfolio, include the interview‑ready explanation, and you’ll have a strong talking point for any **software‑engineering** interview.

Happy coding & good luck on your next interview!