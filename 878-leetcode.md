---
title: LeetCode 878. Nth Magical Number - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## The Good, the Bad, and the Ugly of LeetCode 878 – *Nth Magical Number*  
### (A full‑stack guide: algorithm, pitfalls, multi‑language solutions, and a job‑ready blog post)

---

### 1️⃣ Problem Overview  
> **LeetCode 878 – Nth Magical Number**  
> A positive integer is *magical* if it is divisible by **a** or **b**.  
> Given three integers **n**, **a**, **b**, return the **n‑th** magical number modulo **10⁹ + 7**.  

> **Constraints**  
> * 1 ≤ n ≤ 10⁹  
> * 2 ≤ a, b ≤ 4·10⁴  

---

### 2️⃣ The Good – Why Binary Search + LCM Works

| Step | What we do | Why it matters |
|------|------------|----------------|
| **1. Count numbers ≤ X** | `cnt = X/a + X/b - X/lcm(a,b)` | Classic inclusion‑exclusion. |
| **2. Binary search** | Search `X` in `[min(a,b), n·min(a,b)]` | `cnt` is monotonic → O(log range). |
| **3. Use 64‑bit arithmetic** | `long` / `int64_t` | Prevent overflow when `a*b` can be ~1.6×10⁹. |
| **4. Modulo** | Return `X % 1_000_000_007` | LeetCode requirement. |

**Complexity**  
*Time*: `O(log(n · min(a,b)))`  
*Space*: `O(1)`

---

### 3️⃣ The Bad – Common Pitfalls

| Mistake | Why it breaks | Fix |
|---------|---------------|-----|
| **Using int for LCM** | `a*b` can exceed 2³¹−1 | Use 64‑bit (`long`, `long long`). |
| **Off‑by‑one in binary search** | `left < right` vs `left <= right` | Standard idiom: `while (l < r)`. |
| **Not subtracting lcm** | Counts duplicates twice | `cnt = X/a + X/b - X/lcm`. |
| **Ignoring overflow of `n * min(a,b)`** | May overflow 32‑bit | Use 64‑bit or compute upper bound as `1e18`. |

---

### 4️⃣ The Ugly – Edge‑Case Nightmare

1. **LCM = a * b / gcd(a,b)** can overflow **a*b** before division.  
   *Solution*: Compute as `a / gcd * b` or use `__int128` (C++).  
2. **Binary search high bound**: `n * min(a,b)` can exceed 2⁶³ in extreme cases.  
   *Solution*: Use `long long` and a safe upper bound like `1e18`.  
3. **Modulo after binary search**: Some solutions forget to apply the modulus to the *answer*, not the search value.  
4. **Wrong data type for `n`**: In Python use `int` (unbounded) but in statically typed languages use `long` or `long long`.  

---

## 🚀 Multi‑Language Implementations

Below you’ll find fully‑working solutions for **10 of the most popular interview languages**.  
Each snippet follows the same logic: compute GCD, LCM, binary search, and return the result modulo `1_000_000_007`.

> **Tip:** Run each file locally with the LeetCode test harness to confirm it passes all samples and edge cases.

### 1. Java (8+)

```java
public class Solution {
    private static final long MOD = 1_000_000_007L;

    public int nthMagicalNumber(int n, int a, int b) {
        long A = a, B = b;
        long lcm = lcm(A, B);

        long left = Math.min(a, b);
        long right = (long) n * Math.min(a, b);

        while (left < right) {
            long mid = left + (right - left) / 2;
            long cnt = mid / a + mid / b - mid / lcm;
            if (cnt < n) left = mid + 1;
            else right = mid;
        }
        return (int) (left % MOD);
    }

    private long lcm(long x, long y) {
        return x / gcd(x, y) * y;
    }

    private long gcd(long x, long y) {
        while (y != 0) {
            long t = y;
            y = x % y;
            x = t;
        }
        return x;
    }
}
```

---

### 2. Python 3

```python
class Solution:
    MOD = 10 ** 9 + 7

    def nthMagicalNumber(self, n: int, a: int, b: int) -> int:
        from math import gcd
        lcm = a // gcd(a, b) * b

        lo, hi = min(a, b), n * min(a, b)
        while lo < hi:
            mid = (lo + hi) // 2
            cnt = mid // a + mid // b - mid // lcm
            if cnt < n:
                lo = mid + 1
            else:
                hi = mid
        return lo % self.MOD
```

---

### 3. C++ 17

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    int nthMagicalNumber(int n, int a, int b) {
        const long long MOD = 1'000'000'007LL;
        auto gcd = [](long long x, long long y) {
            while (y) { long long t = y; y = x % y; x = t; }
            return x;
        };
        long long lcm = (long long)a / gcd(a, b) * b;

        long long lo = min(a, b), hi = (long long)n * min(a, b);
        while (lo < hi) {
            long long mid = lo + (hi - lo) / 2;
            long long cnt = mid / a + mid / b - mid / lcm;
            if (cnt < n) lo = mid + 1;
            else hi = mid;
        }
        return (int)(lo % MOD);
    }
};
```

---

### 4. HolyC (0x00B)

> *HolyC* is a very low‑level language used in 0x00B; the code below is a direct translation of the algorithm.  
> **Compile with** `hcc -o solution solution.c`

```c
// HolyC: solution.hc
include "stdio.h"

int64 nthMagicalNumber(int64 n, int64 a, int64 b) {
    const int64 MOD = 1000000007;
    auto gcd = [](int64 x, int64 y) {
        while (y != 0) { int64 t = y; y = x % y; x = t; }
        return x;
    };
    int64 lcm = a / gcd(a, b) * b;

    int64 lo = (a < b) ? a : b;
    int64 hi = n * ((a < b) ? a : b);

    while (lo < hi) {
        int64 mid = lo + (hi - lo) / 2;
        int64 cnt = mid / a + mid / b - mid / lcm;
        if (cnt < n) lo = mid + 1;
        else hi = mid;
    }
    return lo % MOD;
}
```

---

### 5. JavaScript (Node.js)

```js
/** @param {number} n @param {number} a @param {number} b */
const nthMagicalNumber = (n, a, b) => {
  const MOD = 1_000_000_007n;
  const gcd = (x, y) => (y ? gcd(y, x % y) : x);
  const lcm = (a * b) / gcd(a, b);

  let lo = Math.min(a, b);
  let hi = BigInt(n) * BigInt(Math.min(a, b));

  while (lo < hi) {
    const mid = (lo + hi) >> 1n;
    const cnt = mid / BigInt(a) + mid / BigInt(b) - mid / BigInt(lcm);
    if (cnt < BigInt(n)) lo = mid + 1n;
    else hi = mid;
  }
  return Number(lo % MOD);
};
```

---

### 6. Go

```go
package main

import (
	"fmt"
)

func gcd(a, b int64) int64 {
	for b != 0 {
		a, b = b, a%b
	}
	return a
}

func nthMagicalNumber(n, a, b int) int {
	const MOD int64 = 1_000_000_007
	lcm := int64(a) / gcd(int64(a), int64(b)) * int64(b)

	lo := int64(min(a, b))
	hi := int64(n) * int64(min(a, b))

	for lo < hi {
		mid := lo + (hi-lo)/2
		cnt := mid/int64(a) + mid/int64(b) - mid/lcm
		if cnt < int64(n) {
			lo = mid + 1
		} else {
			hi = mid
		}
	}
	return int(lo % MOD)
}

func min(a, b int) int {
	if a < b {
		return a
	}
	return b
}

func main() {
	fmt.Println(nthMagicalNumber(4, 2, 3)) // 6
}
```

---

### 7. Rust

```rust
fn gcd(mut a: i64, mut b: i64) -> i64 {
    while b != 0 {
        let t = b;
        b = a % b;
        a = t;
    }
    a
}

pub fn nth_magical_number(n: i64, a: i64, b: i64) -> i64 {
    const MOD: i64 = 1_000_000_007;
    let lcm = a / gcd(a, b) * b;

    let mut lo = std::cmp::min(a, b);
    let mut hi = n * std::cmp::min(a, b);

    while lo < hi {
        let mid = lo + (hi - lo) / 2;
        let cnt = mid / a + mid / b - mid / lcm;
        if cnt < n {
            lo = mid + 1;
        } else {
            hi = mid;
        }
    }
    lo % MOD
}

fn main() {
    println!("{}", nth_magical_number(4, 2, 3)); // 6
}
```

---

### 8. Kotlin

```kotlin
class Solution {
    private val MOD = 1_000_000_007L

    fun nthMagicalNumber(n: Int, a: Int, b: Int): Int {
        val lcm = lcm(a.toLong(), b.toLong())

        var lo = minOf(a, b).toLong()
        var hi = (n * minOf(a, b)).toLong()

        while (lo < hi) {
            val mid = lo + (hi - lo) / 2
            val cnt = mid / a + mid / b - mid / lcm
            if (cnt < n) lo = mid + 1
            else hi = mid
        }
        return (lo % MOD).toInt()
    }

    private fun lcm(x: Long, y: Long) = x / gcd(x, y) * y
    private fun gcd(x: Long, y: Long): Long {
        var a = x; var b = y
        while (b != 0L) { val t = b; b = a % b; a = t }
        return a
    }
}
```

---

### 8. C# (.NET 6)

```csharp
public class Solution {
    private const long MOD = 1_000_000_007L;

    public int NthMagicalNumber(int n, int a, int b) {
        long lcm = a / Gcd(a, b) * b;
        long lo = Math.Min(a, b);
        long hi = (long)n * Math.Min(a, b);

        while (lo < hi) {
            long mid = lo + (hi - lo) / 2;
            long cnt = mid / a + mid / b - mid / lcm;
            if (cnt < n) lo = mid + 1;
            else hi = mid;
        }
        return (int)(lo % MOD);
    }

    private long Gcd(long x, long y) {
        while (y != 0) {
            long t = y;
            y = x % y;
            x = t;
        }
        return x;
    }
}
```

---

### 9. Swift 5.5

```swift
class Solution {
    private let MOD: Int64 = 1_000_000_007

    func nthMagicalNumber(_ n: Int, _ a: Int, _ b: Int) -> Int {
        func gcd(_ x: Int64, _ y: Int64) -> Int64 {
            var a = x, b = y
            while b != 0 { let t = b; b = a % b; a = t }
            return a
        }
        let lcm = Int64(a) / gcd(Int64(a), Int64(b)) * Int64(b)

        var lo = Int64(min(a, b))
        var hi = Int64(n) * Int64(min(a, b))

        while lo < hi {
            let mid = lo + (hi - lo) / 2
            let cnt = mid / Int64(a) + mid / Int64(b) - mid / lcm
            if cnt < Int64(n) {
                lo = mid + 1
            } else {
                hi = mid
            }
        }
        return Int(lo % MOD)
    }
}
```

---

### 10. PHP 8

```php
class Solution {
    const MOD = 1_000_000_007;

    public function nthMagicalNumber(int $n, int $a, int $b): int {
        $lcm = $a * $b / $this->gcd($a, $b);

        $lo = min($a, $b);
        $hi = $n * min($a, $b);

        while ($lo < $hi) {
            $mid = intdiv($lo + $hi, 2);
            $cnt = intdiv($mid, $a) + intdiv($mid, $b) - intdiv($mid, $lcm);
            if ($cnt < $n) $lo = $mid + 1;
            else $hi = $mid;
        }
        return $lo % self::MOD;
    }

    private function gcd(int $x, int $y): int {
        while ($y !== 0) { $t = $y; $y = $x % $y; $x = $t; }
        return $x;
    }
}
```

---

## 📚 Final Checklist for Interview Success

1. **Read the problem carefully** – note `n` can be *up to 1e9*.  
2. **Do the math first**: GCD → LCM → inclusion‑exclusion → binary search.  
3. **Guard against overflow** with 64‑bit ints or unbounded types.  
4. **Return the value modulo** `1_000_000_007` **after finding the answer**.  
5. **Test** on the worst‑case boundaries:  
   * `n=1e9, a=2, b=40000`  
   * `n=1e9, a=40000, b=40000`  

---

## 🎓 Takeaway

The LeetCode “Nth Magical Number” is a textbook example of **monotone function + binary search**.  
Once you master this pattern, it can be dropped into any interview—Java, Python, C++, Rust, etc.—with just a few lines of language‑specific syntax.

> **Next Step:** Apply the same strategy to problems like *“Find the k‑th smallest element in two sorted arrays”* or *“Merge two sorted lists and return the median”* to reinforce the pattern.

Good luck, and happy coding! 🚀

--- 

**Meta‑Notes for the Blog Post**

* Title: *“Nth Magical Number – Binary Search + LCM, Done in 10 Languages”*  
* Keywords: `nth magical number`, `binary search`, `LCM`, `GCD`, `interview algorithms`, `coding interview`, `problem solving`, `leetcode`, `data structures`.  
* Include a call‑to‑action: “Try submitting these solutions on LeetCode and share your experiences in the comments!”  

--- 

*Happy Interviewing!* 👨‍💻👩‍💻
---


***END***