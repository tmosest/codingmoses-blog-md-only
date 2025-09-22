---
title: LeetCode 810. Chalkboard XOR Game - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # Cracking LeetCode 810 – “Chalkboard XOR Game”  
## The Good, The Bad, and The Ugly  
*(SEO‑optimized guide to help you land your next software‑engineering role)*  

---

### TL;DR

- **Problem**: Two players alternate erasing one number from an array.  
  If a move makes the XOR of the remaining numbers equal to 0, that player loses.  
  If a player starts a turn with XOR = 0, they win immediately.  
- **Answer**:  
  ```text
  Alice wins  ⇔  (total XOR == 0)  OR  (array length is even)
  ```
- **Why it works**: Game‑theoretic parity argument + XOR property.  
- **Complexity**: `O(n)` time, `O(1)` space.  
- **Why you should know it**: Demonstrates fast thinking, math intuition, and concise code – the perfect interview talking point.

---

## 1. Problem Recap

> **LeetCode 810 – Chalkboard XOR Game**  
> Given an array `nums`, Alice and Bob alternately remove a single element.  
> - If a removal causes the XOR of the remaining elements to become **0**, the player who performed that removal **loses**.  
> - If a player starts a turn with the XOR of all remaining elements equal to **0**, they **win immediately**.  
> Return `true` if Alice can force a win, assuming optimal play from both sides.

**Constraints**

| | |
|---|---|
| `1 ≤ nums.length ≤ 1000` | `0 ≤ nums[i] < 2^16` |

---

## 2. Intuitive Insight

> The game is governed by **parity** and the **initial XOR**.

1. **Initial XOR = 0**  
   Alice starts her turn with XOR = 0 → she wins instantly.  
2. **Length is even**  
   Even length → after any move, the number of remaining elements is odd.  
   Because the XOR before the move is non‑zero (otherwise case 1 would have already won), the only way a player can avoid losing is to force the opponent into a position with an even number of elements and XOR ≠ 0, which is a losing position for the opponent.  
3. **Length is odd and initial XOR ≠ 0**  
   Every move leaves an even number of elements.  
   The opponent can always mirror Alice’s strategy and force a win.  
   Hence Alice loses.

> The concise rule: **Alice wins iff the XOR of all numbers is zero or the array length is even.**

---

## 3. Formal Proof Sketch

Let `S` be the XOR of all current numbers, and `n` be the number of remaining elements.

- **Base case**: If `S == 0` at the start of a turn, the player to move wins.  
- **Inductive step**:  
  - If `n` is **even**, the player can remove an element `x` such that `S ^ x != 0`.  
    After removal, `n-1` is odd, and the opponent faces a position that is **losing** by the induction hypothesis.  
  - If `n` is **odd** and `S != 0`, any removal yields an even `n-1`.  
    The opponent can then win by the even‑length strategy above.  
- Therefore the only winning conditions are `S == 0` or `n` even.

---

## 4. Implementation

### Java

```java
class Solution {
    public boolean xorGame(int[] nums) {
        int totalXor = 0;
        for (int val : nums) totalXor ^= val;
        // Alice wins if total XOR is zero or length is even
        return totalXor == 0 || (nums.length % 2 == 0);
    }
}
```

### Python

```python
class Solution:
    def xorGame(self, nums: List[int]) -> bool:
        total_xor = 0
        for v in nums:
            total_xor ^= v
        # Alice wins if total XOR is zero or length is even
        return total_xor == 0 or len(nums) % 2 == 0
```

### C++

```cpp
class Solution {
public:
    bool xorGame(vector<int>& nums) {
        int totalXor = 0;
        for (int v : nums) totalXor ^= v;
        // Alice wins if total XOR is zero or length is even
        return totalXor == 0 || (nums.size() % 2 == 0);
    }
};
```

All three snippets run in **O(n)** time and **O(1)** additional memory.

---

## 5. The Good

| ✅ | Explanation |
|---|--------------|
| **Speed** | One linear scan; no recursion or DP needed. |
| **Clarity** | The condition mirrors the theorem directly; easy to reason about. |
| **Interview impact** | Shows you can reduce a seemingly complex game to a simple parity check. |
| **Reusable pattern** | XOR + parity arguments appear in many Nim‑style interview problems. |

---

## 6. The Bad (Pitfalls to Avoid)

| ⚠️ | Common Mistakes |
|---|-----------------|
| **Assuming XOR==0 ⇒ always win** | Fails when array length is odd and XOR ≠ 0. |
| **Ignoring array length** | Many coders check only XOR, overlooking the parity rule. |
| **Recursive DP** | Overkill; increases space/time unnecessarily. |
| **Off‑by‑one errors** | Mis‑interpreting “even number of elements” vs “even number of moves”. |

---

## 7. The Ugly

| 💢 | Edge Cases & Clarifications |
|---|-----------------------------|
| **Empty array** | Not allowed by constraints (`n ≥ 1`). |
| **All zeros** | XOR = 0 → Alice wins immediately, even if length is odd. |
| **Large numbers** | XOR works up to `2^16` safely; use 32‑bit ints. |
| **Misreading the rule** | “Removing an element that makes XOR = 0 loses” – remember it's the *player who makes that move* who loses, not the one who starts with XOR = 0. |

---

## 8. Interview‑Ready Talking Points

1. **Explain the parity argument** – why even length guarantees a win.  
2. **Show the base case** – starting XOR = 0 gives instant win.  
3. **Mention the theorem** – the concise winning condition.  
4. **Code demonstration** – keep it one‑liner.  
5. **Time & space** – emphasize optimality.  

> *“I solved LeetCode 810 in a single pass by observing that the XOR of all numbers being zero or the array having even length is the exact criterion for Alice’s win. This demonstrates my ability to distill complex game‑theoretic problems into elegant, O(n) solutions.”*

---

## 9. SEO Keywords (for blog ranking)

- Chalkboard XOR Game  
- LeetCode 810 solution  
- XOR game theory  
- Optimal strategy game  
- Game theory interview question  
- Bitwise XOR interview problems  
- Even length game win condition  
- Python Java C++ XOR game  
- Interview coding challenges  

---

## 10. Takeaway

The Chalkboard XOR Game is a textbook example of how **simple math** can transform a seemingly intricate game into a **bite‑size logic puzzle**. By focusing on XOR properties and array parity, you can deliver a solution that is:

- **Fast** (`O(n)`)  
- **Memory‑efficient** (`O(1)`)  
- **Interview‑friendly** – perfect for showcasing analytical thinking.

Use this as a springboard for other Nim‑style problems, and you’ll impress interviewers with both elegance and speed. Good luck cracking that next role! 🚀