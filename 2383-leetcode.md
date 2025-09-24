---
title: LeetCode 2383. Minimum Hours of Training to Win a Competition - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ---

# Minimum Hours of Training to Win a Competition – LeetCode 2383  
*(Java | Python | C++ solutions + interview‑style blog post)*  

> **Keywords**: Minimum Hours of Training, LeetCode 2383, coding interview, greedy algorithm, time complexity, space complexity, Java solution, Python solution, C++ solution, algorithm design

---

## 📌 Problem Summary

You are entering a multi‑round competition with:
- `initialEnergy` – the amount of energy you start with  
- `initialExperience` – the amount of experience you start with  

You will face `n` opponents in order.  
Opponent `i` has:
- `energy[i]` – the energy cost you will lose when defeating them  
- `experience[i]` – the experience you will gain

You win against an opponent if **both** your current energy and experience are **strictly greater** than the opponent’s.  
After each win, your energy decreases by `energy[i]` and your experience increases by `experience[i]`.

Before the competition you can train for some hours.  
Each hour you can **either** increase your initial energy by 1 **or** your initial experience by 1.  
Return the **minimum** number of training hours required to defeat all opponents.

---

## 💡 Core Insight

The problem can be solved with a *greedy simulation*:

1. **Experience**  
   - While iterating through the opponents, if your current experience is **≤** the opponent’s experience, you must train enough hours to make it **>** that opponent.  
   - The exact amount needed is `opponentExp - currentExp + 1`.  
   - Add this to the answer, update `currentExp`, and then add the opponent’s experience.

2. **Energy**  
   - After handling experience, compute the **total energy** required to survive all battles:  
     `requiredEnergy = sum(energy) + 1`.  
   - If your initial energy is already larger, you need no training for energy.  
   - Otherwise, you need `requiredEnergy - initialEnergy` training hours.

This approach is optimal because:
- For experience, you only train when you would lose a round, and the amount added is the minimum to win that round.
- For energy, you can’t beat any opponent unless your energy surpasses the cumulative sum of all energy costs.  
  Thus training exactly enough to satisfy this condition is optimal.

---

## 🏃‍♂️ Algorithms & Complexity

| Language | Algorithm | Time | Space |
|----------|-----------|------|-------|
| Java | One pass through `experience` + O(1) sum of `energy` | **O(n)** | **O(1)** |
| Python | Same as Java | **O(n)** | **O(1)** |
| C++ | Same as Java | **O(n)** | **O(1)** |

All solutions run in linear time and constant auxiliary space, easily handling the constraint `n ≤ 100`.

---

## 📄 Code Implementations

### 1️⃣ Java

```java
/**
 * Minimum Hours of Training to Win a Competition - LeetCode 2383
 *
 * Time Complexity: O(n)
 * Space Complexity: O(1)
 */
class Solution {
    public int minNumberOfHours(int initialEnergy, int initialExperience, int[] energy, int[] experience) {
        // 1. Compute required extra energy
        int totalEnergyNeeded = 0;
        for (int e : energy) totalEnergyNeeded += e;
        int energyHours = Math.max(0, totalEnergyNeeded - initialEnergy + 1);

        // 2. Simulate the experience while counting necessary training hours
        int experienceHours = 0;
        int currentExp = initialExperience;
        for (int exp : experience) {
            if (currentExp <= exp) {
                int diff = exp - currentExp + 1;
                experienceHours += diff;
                currentExp += diff;      // Train before facing this opponent
            }
            currentExp += exp;           // Gain experience after the win
        }

        return energyHours + experienceHours;
    }
}
```

> **Tip** – Use `Math.max` to avoid negative training hours for energy.

---

### 2️⃣ Python

```python
"""
Minimum Hours of Training to Win a Competition - LeetCode 2383
"""

class Solution:
    def minNumberOfHours(self, initialEnergy: int, initialExperience: int,
                         energy: list[int], experience: list[int]) -> int:
        # 1. Energy: sum of all costs + 1
        total_energy_needed = sum(energy) + 1
        energy_hours = max(0, total_energy_needed - initialEnergy)

        # 2. Experience simulation
        experience_hours = 0
        curr_exp = initialExperience
        for exp in experience:
            if curr_exp <= exp:
                diff = exp - curr_exp + 1
                experience_hours += diff
                curr_exp += diff          # Train before this round
            curr_exp += exp                # Gain after the win

        return energy_hours + experience_hours
```

> **Why `+1` for energy?**  
> To win the **last** opponent, your energy must be strictly greater than the *sum* of all `energy[i]`.  
> Adding 1 guarantees strict inequality.

---

### 3️⃣ C++

```cpp
/**
 * Minimum Hours of Training to Win a Competition - LeetCode 2383
 *
 * Time:  O(n)
 * Space: O(1)
 */
class Solution {
public:
    int minNumberOfHours(int initialEnergy, int initialExperience,
                         vector<int>& energy, vector<int>& experience) {
        // Required extra energy
        int totalEnergy = 0;
        for (int e : energy) totalEnergy += e;
        int energyHours = max(0, totalEnergy - initialEnergy + 1);

        // Experience simulation
        int experienceHours = 0;
        int currExp = initialExperience;
        for (int exp : experience) {
            if (currExp <= exp) {
                int diff = exp - currExp + 1;
                experienceHours += diff;
                currExp += diff;          // Train before this opponent
            }
            currExp += exp;                // Gain after win
        }

        return energyHours + experienceHours;
    }
};
```

> **C++11+** – `vector<int>` and `max` from `<algorithm>` are sufficient.

---

## ⚠️ Common Pitfalls

| Pitfall | Why it Happens | Fix |
|---------|----------------|-----|
| **Missing the `+1` for energy** | Thinking “sum of energy” is enough. | Always add 1 to ensure strict inequality. |
| **Training experience after adding opponent’s experience** | Wrong order leads to unnecessary training. | Check and train **before** adding opponent’s experience. |
| **Using `<=` instead of `<` in experience comparison** | Confusion between “greater than” vs “greater than or equal”. | Keep `<=` check because we need strictly greater. |
| **Integer overflow (not an issue with constraints)** | With larger constraints, summing 100 * 100 could overflow 32‑bit. | Use `long long` in C++ or `int` in Java/Python (they auto‑extend). |

---

## 🔄 Variations & Extensions

1. **Random Order of Opponents** – If the order can change, a dynamic programming solution may be needed.
2. **Multiple Training Options** – Instead of 1‑point increments, allow different training benefits; use greedy or DP accordingly.
3. **Energy Recovery** – Suppose you recover a fixed amount after each round; modify the simulation to incorporate it.

---

## 🚀 Interview Tips

1. **Clarify the “strictly greater” rule** – It changes the condition from `>=` to `>`.  
2. **Explain the two separate sub‑problems** – Experience and Energy.  
3. **Show the greedy proof** – “We only train when we cannot win; the amount added is minimal.”  
4. **Write clean code** – Use descriptive variable names (`totalEnergyNeeded`, `experienceHours`).  
5. **Discuss complexity** – Mention linear time and constant space.  

---

## 🎓 Takeaway

The “Minimum Hours of Training to Win a Competition” problem is a classic *greedy simulation* that balances two independent resources.  
By treating experience and energy separately, you can derive a clean, optimal algorithm that runs in O(n) time.  
Mastering this pattern will help you tackle many interview questions where resource constraints and ordering matter.

Happy coding, and good luck on your next interview! 🚀

---