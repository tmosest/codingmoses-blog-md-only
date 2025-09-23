---
title: LeetCode 3591. Check if Any Element Has Prime Frequency - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 3591 – Check if Any Element Has Prime Frequency  
*(Easy)*  

| Language | Time | Space |
|----------|------|-------|
| **Java** | O(n) | O(n) |
| **Python** | O(n) | O(n) |
| **C++** | O(n) | O(n) |

> **Why it matters:**  
> * In an interview you’ll be asked to count frequencies and then test a property of those counts.  
> * This problem is a classic “frequency + helper function” question that showcases your data‑structure knowledge and your ability to write clean, efficient helper functions (prime test).  

---

## ✅ 1. Quick Reference – “Cheat Sheet” Code

### Java

```java
import java.util.HashMap;

public class Solution {
    public boolean checkPrimeFrequency(int[] nums) {
        // Frequency map
        HashMap<Integer, Integer> freq = new HashMap<>();
        for (int x : nums) {
            freq.put(x, freq.getOrDefault(x, 0) + 1);
        }

        // Helper to check if a count is prime
        for (int count : freq.values()) {
            if (isPrime(count)) return true;
        }
        return false;
    }

    private boolean isPrime(int n) {
        if (n <= 1) return false;          // 1 and 0 are not prime
        if (n <= 3) return true;           // 2 and 3 are prime
        if (n % 2 == 0 || n % 3 == 0) return false;
        for (int i = 5; i * i <= n; i += 6) {
            if (n % i == 0 || n % (i + 2) == 0) return false;
        }
        return true;
    }
}
```

### Python

```python
class Solution:
    def checkPrimeFrequency(self, nums: List[int]) -> bool:
        from collections import Counter
        freq = Counter(nums)

        def is_prime(n: int) -> bool:
            if n <= 1:          # 0 and 1 are not prime
                return False
            if n <= 3:          # 2, 3
                return True
            if n % 2 == 0 or n % 3 == 0:
                return False
            i = 5
            while i * i <= n:
                if n % i == 0 or n % (i + 2) == 0:
                    return False
                i += 6
            return True

        return any(is_prime(c) for c in freq.values())
```

### C++

```cpp
class Solution {
public:
    bool checkPrimeFrequency(vector<int>& nums) {
        unordered_map<int, int> freq;
        for (int x : nums) freq[x]++;          // Count frequencies

        for (auto &[val, cnt] : freq) {
            if (isPrime(cnt)) return true;     // Early return
        }
        return false;
    }

private:
    bool isPrime(int n) {
        if (n <= 1) return false;
        if (n <= 3) return true;
        if (n % 2 == 0 || n % 3 == 0) return false;
        for (int i = 5; i * i <= n; i += 6) {
            if (n % i == 0 || n % (i + 2) == 0) return false;
        }
        return true;
    }
};
```

> **Note:**  
> * All three implementations use the same pattern:  
>   * Build a frequency dictionary/map.  
>   * Scan the frequencies and test each for primality.  
> * The prime test is a classic “6k ± 1” optimization that cuts down on unnecessary checks.

---

## 📝 2. Problem Walk‑through

### Problem Recap

> **Input** – An integer array `nums` (1 ≤ `nums.length` ≤ 100, 0 ≤ `nums[i]` ≤ 100).  
> **Output** – `true` if **any** element in the array appears a prime number of times; otherwise `false`.

### Key Observations

1. **Only frequencies matter** – The actual values in `nums` are irrelevant once we know how many times each appears.
2. **Small array** – With a maximum length of 100, the solution can afford O(n²) checks, but we’ll still aim for O(n).
3. **Prime test range** – Frequencies can range from 1 to 100 (worst case: all elements equal). So a pre‑computed prime list or a simple trial division is fast enough.

### Common Pitfalls

| Bad Practice | Why it’s bad | Fix |
|--------------|--------------|-----|
| **Checking every possible integer as prime** | Unnecessary work for 0‑100 | Use a small static list `{2,3,5,7}` or a quick trial division |
| **Iterating through the array twice** | Still O(n), but wasteful | Count frequencies in one pass, then scan counts once |
| **Using a recursive prime check** | Stack overflow risk + extra overhead | Use iterative loop with early exit |

---

## 📘 3. The “Good, Bad, and Ugly” Breakdown

### Good – What the solution does right

| ✅ | Detail |
|----|--------|
| **Linear time** | `O(n)` because we traverse the array once for counting and once for checking. |
| **Space‑efficient** | `O(n)` but at most 101 keys (numbers 0‑100). |
| **Reusable helper** | The `isPrime()` function is generic and can be used elsewhere. |
| **Early exit** | As soon as a prime frequency is found we stop scanning, saving time. |

### Bad – Where the solution could still improve

| ⚠️ | Detail |
|-----|--------|
| **Hard‑coded prime list** | For larger input ranges a static list becomes insufficient. |
| **Not caching prime results** | If many frequencies repeat, recomputing primality is wasteful. |
| **No input validation** | In production code you’d guard against null or negative lengths. |

### Ugly – What could go wrong if we ignore edge cases

| 😈 | Detail |
|-----|--------|
| **`nums` is empty** | The constraints forbid this, but defensive programming would return `false`. |
| **Negative values** | Constraints disallow, but a real interview may test for robustness. |
| **Large frequency counts (≥10⁵)** | The current prime test would still work, but would be slower. A sieve up to √maxFreq could be faster. |

---

## 🎯 4. Interview‑Ready Tips

1. **Explain your plan first** – “I’ll count frequencies using a hash map, then check each count for primality.”  
2. **Show a simple `isPrime` helper** – Use the 6k±1 optimization; it's short and demonstrates algorithmic thinking.  
3. **Mention early exit** – “As soon as we find a prime frequency we can return true.”  
4. **Talk about edge cases** – “If the array is all distinct, every count is 1, which is not prime.”  
5. **If time permits, discuss alternative approaches** – e.g., pre‑computing a sieve of primes up to 100.

---

## 📚 5. Bonus: Why This Problem Is a Good Resume‑Builder

* **Data structures** – Hash maps / frequency tables.  
* **Number theory** – Prime checks.  
* **Time‑space trade‑offs** – Early exit, linear pass.  
* **Coding style** – Clean helper functions, concise logic.  

When you discuss this solution on LinkedIn or during an interview, you’ll demonstrate:  

- *Strong understanding of basic algorithms.*  
- *Ability to write clean, maintainable code.*  
- *Practical problem‑solving under constraints.*  

---  

## ✍️ 6. Blog Article – “The Good, the Bad, and the Ugly of LeetCode Problem 3591”

### Title  
**“The Good, the Bad, and the Ugly of LeetCode 3591 – A Job‑Ready Guide”**

### Meta Description  
Learn how to crack LeetCode problem 3591 (“Check if Any Element Has Prime Frequency”) with Java, Python, and C++ solutions, plus interview insights and SEO‑friendly tips to boost your tech resume.

### Outline

1. **Hook** – “Ever seen a problem that’s *almost* trivial but hidden behind a prime number twist?”  
2. **Problem Summary** – Quick read of constraints and example.  
3. **Why It Matters** – Frequency counting is a staple in interviews; prime checks show algorithmic depth.  
4. **The “Good” – Efficient Solution** – Discuss linear algorithm, O(n) time, O(n) space.  
5. **The “Bad” – Common Misses** – Hard‑coding primes, re‑checking frequencies, ignoring early exit.  
6. **The “Ugly” – Edge Cases & Defensive Coding** – Empty arrays, negative numbers, large inputs.  
7. **Full Code Snippets** – Java, Python, C++ (as shown above).  
8. **Interview‑Ready Narrative** – How to explain your solution on a whiteboard.  
9. **Resume Hook** – “Implemented a frequency‑based prime checker in O(n) time.”  
10. **SEO Keywords** – LeetCode 3591, check prime frequency, interview coding, algorithm analysis, Java Python C++ solutions, resume tips, coding interview prep.  
11. **Call‑to‑Action** – “Practice this problem, share your solution, and land that job!”

### Sample Blog Post Excerpt

> **The Good** –  
> “The key to a fast solution is *count‑once, check‑once.* We build a hash map of frequencies and then iterate over the counts, stopping immediately when we hit a prime. This gives us an elegant O(n) algorithm with minimal overhead.”
>
> **The Bad** –  
> “Many candidates fall into the trap of hard‑coding a list of small primes (2, 3, 5, 7). While this works for the LeetCode constraints, it breaks if the array contains 100‑sized frequencies. A general prime test is safer and more scalable.”
>
> **The Ugly** –  
> “What if the array is empty or contains negative numbers? LeetCode’s constraints say no, but a production‑grade function should handle these gracefully. A defensive check at the start can save bugs later.”

### Final Thought

> By mastering this “prime frequency” problem, you’re not just solving a single LeetCode challenge—you’re demonstrating key interview skills: **data‑structure fluency, algorithmic optimization, and defensive coding**. Pair this knowledge with a polished résumé that highlights your O(n) solution, and you’ll have a strong competitive edge in your next tech interview.

---

### 📌 Quick Reference for Job Hunters

| What recruiters look for | How to showcase it |
|--------------------------|--------------------|
| **Efficient algorithms** | Mention O(n) time, O(n) space |
| **Clean code** | Provide well‑commented, modular functions |
| **Problem understanding** | Explain the frequency + prime insight |
| **Technical communication** | Walk through the solution on a whiteboard or in a blog |

Drop a link to this post on your LinkedIn, include the code in a personal GitHub repo, and let the *Good, Bad, Ugly* narrative turn your interview into a success story.

---  

**Happy coding, and best of luck landing your dream job!** 🚀

---  

*© 2024 Coding Insights, All Rights Reserved.*  

--- 

*If you'd like a deeper dive into the 6k ± 1 prime optimization or alternative solutions (like a sieve of Eratosthenes), let me know!*