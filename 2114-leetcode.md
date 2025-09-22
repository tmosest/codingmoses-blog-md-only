---
title: LeetCode 2114. Maximum Number of Words Found in Sentences - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

## 1.  Problem Restatement  
**Leetcode 2114 – Maximum Number of Words Found in Sentences**  
Given an array `sentences` where each element is a single sentence composed of lowercase English letters and spaces, return the maximum number of words that appear in a single sentence.  
*All sentences are guaranteed to be well‑formed:* no leading/trailing spaces and words are separated by a **single** space.

---

## 2.  Solution Overview  
The number of words in a sentence is simply the number of spaces plus one.  
So for each string we can either

1. **Split** on `" "` and take the length of the resulting array, or  
2. **Count** the spaces directly and add one.  

Both give the same result, but counting spaces uses *O(1)* auxiliary space and is slightly faster in practice.  
We keep a running maximum and return it at the end.

| Approach | Time | Space |
|----------|------|-------|
| Split | `O(total_characters)` | `O(number_of_words)` (temporary array) |
| Count spaces | `O(total_characters)` | `O(1)` |

The constraints (`≤ 100` sentences, each ≤ 100 chars) mean either approach will run instantly, but the space‑optimal method is the classic interview‑go‑to solution.

---

## 3.  Reference Code  

### Java
```java
/**
 *  Leetcode 2114 – Maximum Number of Words Found in Sentences
 *  Time:  O(total number of characters)
 *  Space: O(1) – apart from the input array
 */
class Solution {
    public int mostWordsFound(String[] sentences) {
        int maxWords = 0;
        for (String sentence : sentences) {
            // Count spaces
            int spaces = 0;
            for (int i = 0; i < sentence.length(); i++) {
                if (sentence.charAt(i) == ' ') spaces++;
            }
            int words = spaces + 1;        // one more word than spaces
            if (words > maxWords) maxWords = words;
        }
        return maxWords;
    }
}
```

> **Alternative (split‑based)**  
> `int words = sentence.split(" ").length;`  
> Works fine but creates a temporary array.

---

### Python
```python
# Leetcode 2114 – Maximum Number of Words Found in Sentences
# Time: O(total characters)
# Space: O(1) – only counters

class Solution:
    def mostWordsFound(self, sentences: List[str]) -> int:
        max_words = 0
        for s in sentences:
            # Count spaces directly
            spaces = s.count(' ')
            words = spaces + 1
            max_words = max(max_words, words)
        return max_words
```

> The `str.count(' ')` is a built‑in fast implementation that loops internally.

---

### C++
```cpp
// Leetcode 2114 – Maximum Number of Words Found in Sentences
// Time:  O(total characters)
// Space: O(1)

class Solution {
public:
    int mostWordsFound(vector<string>& sentences) {
        int maxWords = 0;
        for (const string &s : sentences) {
            int spaces = 0;
            for (char c : s)
                if (c == ' ') ++spaces;
            int words = spaces + 1;
            maxWords = max(maxWords, words);
        }
        return maxWords;
    }
};
```

> Modern C++ could use `std::count` but the explicit loop keeps the code portable.

---

## 4.  Blog Post – “The Good, the Bad, and the Ugly of Leetcode 2114”

### Title  
**“Leetcode 2114 – Mastering Maximum Words in Sentences: A Job‑Ready Guide”**

### Meta Description  
Solve Leetcode 2114 in Java, Python, and C++ with an optimal, interview‑friendly algorithm. Understand the pros, cons, and common pitfalls. Perfect for coding interview prep and landing a software job.

---

#### 1.  Introduction  

When you land a technical interview, the problems you choose to master speak volumes about your coding chops. **Leetcode 2114 – “Maximum Number of Words Found in Sentences”** is a classic *Easy* problem that tests a candidate’s ability to translate a simple statement into an efficient algorithm.  

In this post, we’ll:

- Walk through the simplest optimal solution.  
- Show implementation in Java, Python, and C++.  
- Discuss the *good*, *bad*, and *ugly* aspects of the problem.  
- Offer interview‑friendly talking points that can help you land that job.

---

#### 2.  Problem Recap  

> **Goal**: Given an array of sentences, return the maximum number of words in any single sentence.  
> **Constraints**:  
> - `1 ≤ sentences.length ≤ 100`  
> - `1 ≤ sentences[i].length ≤ 100`  
> - Sentences contain only lowercase letters and spaces, with exactly one space between words and no leading/trailing spaces.

---

#### 3.  The “Good” – Why This Problem Is Great  

| Aspect | Why It’s Good |
|--------|---------------|
| **Conceptual Clarity** | Turns a word‑counting problem into a simple string‑parsing exercise. |
| **Time Complexity** | Linear in the total number of characters – O(n). |
| **Space Complexity** | Constant auxiliary space when counting spaces; no extra data structures. |
| **Interview Takeaway** | Demonstrates your ability to think in terms of *character streams* and avoid unnecessary allocations. |
| **Languages Covered** | Solutions in Java, Python, C++ showcase cross‑language skill. |

---

#### 4.  The “Bad” – Common Pitfalls to Avoid  

| Pitfall | Explanation | Mitigation |
|---------|-------------|------------|
| **Using `split` indiscriminately** | Creates a temporary array, adds overhead. | Prefer direct space counting. |
| **Assuming empty sentences** | The problem guarantees at least one word, but a defensive coder should handle an empty string to avoid a division by zero or negative word count. | `if (s.isEmpty()) return 0;` |
| **Off‑by‑one errors** | Forgetting that the number of words is `spaces + 1`. | Test with single‑word and multi‑word sentences. |
| **Ignoring character encoding** | Rare here, but for Unicode spaces you’d need a more robust check. | Stick to ASCII `' '` as specified. |

---

#### 5.  The “Ugly” – Edge Cases & What Not to Do  

- **Leading/Trailing Spaces**: If input data is not well‑formed, `split(" ")` will produce empty strings that inflate the count.  
- **Multiple Consecutive Spaces**: A naive `split` on `" "` will treat consecutive spaces as multiple delimiters, giving incorrect counts.  
- **Very Long Sentences**: Though constraints limit length to 100, a generic solution should be robust enough for larger inputs.  

**Bottom line:** When you’re in an interview, clarify the constraints first. If the problem statement says “single space”, you can safely rely on that assumption.

---

#### 6.  Interview Talking Points  

1. **Why `spaces + 1`?**  
   *Explain the invariant that words = spaces + 1 given a well‑formed sentence.*

2. **Time vs. Space Trade‑off**  
   *Discuss how `split` is simple but uses extra memory.*

3. **Scalability**  
   *Mention that the linear time solution will scale to millions of characters if the constraints were relaxed.*

4. **Testing Strategy**  
   *Show sample test cases: single word, two words, maximum length, boundary cases.*

5. **Language‑Specific Tips**  
   - *Java:* `String#charAt` loop is faster than `split`.  
   - *Python:* `str.count` is highly optimized.  
   - *C++:* Manual loop is straightforward; could also use `std::count`.

---

#### 7.  Bonus – One‑Liner Variants  

| Language | One‑liner |
|----------|-----------|
| Java | `int max = Arrays.stream(sentences).mapToInt(s -> s.split(" ").length).max().orElse(0);` |
| Python | `max(len(s.split()) for s in sentences)` |
| C++ | `int maxWords = 0; for (auto& s : sentences) maxWords = max(maxWords, (int)s.split(' ').size());` *(requires a split helper)* |

> **Warning:** One‑liners can be elegant but may hide complexity; stick to readable code in interviews.

---

#### 8.  Conclusion  

Leetcode 2114 is a deceptively simple problem that, when solved correctly, showcases:

- **Understanding of basic string operations**  
- **Efficient use of loops and counters**  
- **Attention to edge cases and constraints**  
- **Clean, readable code across multiple languages**

By mastering the “good, the bad, and the ugly,” you’ll be ready to walk into any coding interview with confidence. Plus, you now have a portfolio‑ready snippet in Java, Python, and C++ to flaunt on your resume or GitHub.

Happy coding, and may your interviews be smooth! 🚀  

--- 

> **SEO Keywords:** Leetcode 2114, maximum number of words found in sentences, interview question, Java solution, Python solution, C++ solution, coding interview, job interview prep, algorithm interview, time complexity, space complexity.  

---