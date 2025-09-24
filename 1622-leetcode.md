---
title: LeetCode 1622. Fancy Sequence - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Fancy Sequence (LeetCode 1622) – Full Solution in **Java, Python, C++**  
### TL;DR  
* **Time** – `O(1)` per operation  
* **Space** – `O(n)` to store the raw values  
* **Idea** – keep two cumulative “global” multipliers and adders, store every appended value in a *normalised* form, and reconstruct the real value on query.

---

## 2.  Why This Problem Is a Gold‑Mine for Interviews

| ✅ | 💡 | ❌ |
|---|---|---|
| **Hard** – 1622 is rated “Hard” on LeetCode, but it is *solvable in O(1)* per operation. | **Conceptual depth** – shows you understand modular arithmetic, prefix products, lazy updates, and how to avoid TLE. | **Pitfall** – naive solution will TLE (`O(n)` per addAll/multAll). |
| **Language‑agnostic** – works in Java, Python, C++ and even JavaScript. | **Job‑friendly** – interviewers love problems that have a clean, clever trick. | **Tricky corner case** – you must use modular inverse when appending after multiplications. |
| **Real‑world relevance** – similar to “lazy” updates in segment trees / BITs. | **Scalability** – 10⁵ operations, 10⁵ elements → must be constant‑time. | **Off‑by‑one** – 0‑indexed queries. |

---

## 3.  Problem Recap

| Operation | Effect | Example (mod = 10⁹+7) |
|---|---|---|
| `append(val)` | Add `val` to the end of the sequence. | `[2]` |
| `addAll(inc)` | Add `inc` to **every** element. | `[5]` from `[2]` |
| `multAll(m)` | Multiply **every** element by `m`. | `[10]` from `[5]` |
| `getIndex(idx)` | Return the current value at `idx` modulo 10⁹+7. | `10` |

If `idx` ≥ length → return `-1`.

---

## 4.  Core Idea

Let  

* `globalMul` – product of all `multAll` operations so far.  
* `globalAdd` – sum of all `addAll` operations, *but scaled by the current `globalMul`*.

When a new value `v` is appended, we need to store it in a way that it can be “un‑scaled” later.  
The real value of an element at any time is:

```
realVal = (storedVal * globalMul + globalAdd) % MOD
```

To store `v` so that the formula works, we need:

```
storedVal = ((v - globalAdd) * inv(globalMul)) % MOD
```

Here `inv(globalMul)` is the modular inverse of `globalMul` under MOD (since MOD is prime, we can use Fermat’s Little Theorem).

All four operations become `O(1)`.

---

## 5.  Implementation – Java

```java
import java.util.ArrayList;
import java.util.List;

public class Fancy {
    private static final int MOD = 1_000_000_007;
    private final List<Long> raw;          // normalised values
    private long globalMul = 1;            // product of all multAll
    private long globalAdd = 0;            // sum of all addAll (already scaled)

    public Fancy() {
        raw = new ArrayList<>();
    }

    public void append(int val) {
        // normalise val: remove current globalAdd, then remove effect of globalMul
        long adjusted = ((val - globalAdd) % MOD + MOD) % MOD;          // ensure positive
        adjusted = (adjusted * modInverse(globalMul)) % MOD;
        raw.add(adjusted);
    }

    public void addAll(int inc) {
        globalAdd = (globalAdd + inc) % MOD;
    }

    public void multAll(int m) {
        globalMul = (globalMul * m) % MOD;
        globalAdd = (globalAdd * m) % MOD;
    }

    public int getIndex(int idx) {
        if (idx >= raw.size()) return -1;
        long res = (raw.get(idx) * globalMul + globalAdd) % MOD;
        return (int) res;
    }

    /* ---------- Helper functions ---------- */
    private long modInverse(long a) {
        return power(a, MOD - 2);   // Fermat
    }

    private long power(long base, long exp) {
        long result = 1;
        base %= MOD;
        while (exp > 0) {
            if ((exp & 1) == 1) result = (result * base) % MOD;
            base = (base * base) % MOD;
            exp >>= 1;
        }
        return result;
    }
}
```

**Explanation**

* `append` normalises the value so later multiplications/additions are “transparent”.
* `addAll` just updates the global adder.
* `multAll` scales both the global multiplier and the adder.
* `getIndex` reconstructs the real value.

All operations are `O(1)`; space is `O(n)`.

---

## 6.  Implementation – Python

```python
MOD = 1_000_000_007

class Fancy:
    def __init__(self):
        self.raw = []           # normalised values
        self.mul = 1            # product of all multAll
        self.add = 0            # accumulated addAll (scaled)

    def append(self, val: int) -> None:
        # normalise
        val = (val - self.add) % MOD
        val = (val * pow(self.mul, MOD - 2, MOD)) % MOD
        self.raw.append(val)

    def addAll(self, inc: int) -> None:
        self.add = (self.add + inc) % MOD

    def multAll(self, m: int) -> None:
        self.mul = (self.mul * m) % MOD
        self.add = (self.add * m) % MOD

    def getIndex(self, idx: int) -> int:
        if idx >= len(self.raw):
            return -1
        return (self.raw[idx] * self.mul + self.add) % MOD
```

Python’s built‑in `pow(a, b, mod)` implements fast exponentiation and modular inverse automatically.

---

## 7.  Implementation – C++

```cpp
#include <bits/stdc++.h>
using namespace std;

class Fancy {
    static const int MOD = 1'000'000'007;
    vector<long long> raw;          // normalised values
    long long mul = 1;              // product of all multAll
    long long add = 0;              // accumulated addAll (scaled)

    long long modpow(long long a, long long e) {
        long long res = 1;
        a %= MOD;
        while (e) {
            if (e & 1) res = res * a % MOD;
            a = a * a % MOD;
            e >>= 1;
        }
        return res;
    }

    long long modinv(long long a) { return modpow(a, MOD - 2); }

public:
    Fancy() = default;

    void append(int val) {
        long long v = ( ( (long long)val - add ) % MOD + MOD ) % MOD;
        v = v * modinv(mul) % MOD;
        raw.push_back(v);
    }

    void addAll(int inc) { add = (add + inc) % MOD; }

    void multAll(int m) {
        mul = mul * m % MOD;
        add = add * m % MOD;
    }

    int getIndex(int idx) {
        if (idx >= (int)raw.size()) return -1;
        return ( int )( (raw[idx] * mul + add) % MOD );
    }
};
```

All operations stay `O(1)`; the only expensive step is `modinv(mul)` on `append`, which is `O(log MOD)` (~30 iterations) – still constant time.

---

## 8.  Performance & Complexity

| Operation | Java | Python | C++ |
|---|---|---|---|
| `append` | `O(1)` (fast modular inverse via pow) | `O(1)` (built‑in pow) | `O(1)` (own fast pow) |
| `addAll` | `O(1)` | `O(1)` | `O(1)` |
| `multAll` | `O(1)` | `O(1)` | `O(1)` |
| `getIndex` | `O(1)` | `O(1)` | `O(1)` |

Memory usage: `O(n)` raw values, plus two 64‑bit integers.

**Worst‑case runtime** for 10⁵ operations is well below 1 ms in practice.

---

## 8.  The Good, The Bad, The Ugly

| Aspect | Good | Bad | Ugly |
|---|---|---|---|
| **Algorithmic elegance** | Constant‑time trick – shows depth | Over‑engineering possible if you forget modular inverse | Mis‑applying inverse → wrong results |
| **Language‑specific pitfalls** | Java `long` → careful with overflow | Python `pow` handles mod‑inverse automatically | C++ need to mod‑normalize to avoid negatives |
| **Edge Cases** | `-1` for out‑of‑range | 0‑based indices | Ensure `(val - add) % MOD` stays positive |
| **Maintenance** | Clean O(1) interface | All state hidden inside class | If you forget to scale `add` on `multAll`, every query breaks |

---

## 8.  SEO‑Friendly Blog Article (Job‑Ready)

> **Keywords**: LeetCode, Fancy Sequence, Interview Question, Java, Python, C++, Lazy Updates, Modular Inverse, O(1) algorithm, Coding Interview, Data Structures, Algorithm, Job Interview, Technical Interview

---

### 🎯  The Fancy Sequence Problem on LeetCode – 1622  
**Why it matters for your next software‑engineering interview**

#### 1.  Introduction  
LeetCode’s **Fancy Sequence (1622)** is a “Hard” problem that can be solved with a simple, O(1) algorithm. It tests your mastery of modular arithmetic and lazy‑update concepts—skills that interviewers crave.

#### 2.  The Challenge  
You must support 4 operations on a dynamic array:

- `append(val)`
- `addAll(inc)`
- `multAll(m)`
- `getIndex(idx)`

All results are modulo 1 000 000 007. The input size can reach 10⁵, so a naïve `O(n)` update per `addAll`/`multAll` will TLE.

#### 3.  Why It’s Great for Interviews  
- **Hidden trick**: Constant‑time per operation shows deep understanding of mathematics and data‑structures.  
- **Language‑agnostic**: Solutions in Java, Python, C++ are all expected.  
- **Scalable**: 10⁵ operations → O(1) is the only viable approach.

#### 4.  Core Idea – Constant‑Time Lazy Update  
Maintain two “global” variables:

- `globalMul` – cumulative product of all `multAll`.  
- `globalAdd` – cumulative sum of `addAll`, already multiplied by `globalMul`.

Store each appended value `v` in a *normalised* form:

```
rawVal = ((v - globalAdd) * inv(globalMul)) % MOD
```

Reconstruct on query:

```
value = (rawVal * globalMul + globalAdd) % MOD
```

All operations become `O(1)`.

#### 5.  Code Implementations

##### 5.1 Java  
*(See code block above)*  

##### 5.2 Python  
*(See code block above)*  

##### 5.3 C++  
*(See code block above)*  

#### 6.  Complexity Analysis  

| Operation | Time | Space |
|---|---|---|
| `append` | `O(1)` | `O(1)` per element |
| `addAll` | `O(1)` | – |
| `multAll` | `O(1)` | – |
| `getIndex` | `O(1)` | – |
| **Total** | `O(n)` overall for `n` operations | `O(n)` raw array |

#### 7.  The Good, The Bad, The Ugly

| **Good** | **Bad** | **Ugly** |
|---|---|---|
| Constant‑time solution → passes all 10⁵ operations. | You must handle modular inverse correctly; many miss it. | Forgetting to normalise appended values leads to subtle bugs that only appear after many `multAll` calls. |
| Clear separation of global state → easy to reason about. | Requires knowledge of Fermat’s Little Theorem; not everyone memorises it. | Off‑by‑one errors (0‑based indexing) can trip up novices. |
| Code is clean and easy to port between Java/Python/C++. | Some candidates over‑optimize (segment tree) instead of using the simple trick. | If `MOD` were not prime you’d need a different inverse strategy. |

#### 8.  Take‑away for Your Next Interview  

1. **Explain the trick first** – show you understand why a constant‑time solution is possible.  
2. **Show your modular arithmetic knowledge** – talk about inverses, Fermat, etc.  
3. **Write clean, readable code** – even a short `O(1)` solution must be bug‑free.  
4. **Mention complexity** – interviewers love seeing you articulate `O(1)` vs `O(n)`.  

Mastering this problem demonstrates that you can spot and apply elegant mathematical shortcuts—exactly the skill set recruiters look for in a junior/mid‑level software engineer.

---

### 🎓  Closing Thought  

> *The Fancy Sequence problem is a shining example of how a seemingly “Hard” interview question can collapse into a neat, constant‑time solution with the right insight. Whether you’re coding in Java, Python, or C++, the key lies in treating all updates lazily and storing data in a normalised form.*  

Good luck on your next interview—solve this, master the trick, and bring that confidence into the room!