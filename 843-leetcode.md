---
title: LeetCode 843. Guess the Word - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Guess the Word – 843**  
*(Java – interactive “Master” interface)*  

---

## 1.  Problem Recap  

You are given a *wordlist* of up to 10 000 6‑letter strings (all the same length).  
One of them is the **secret word**.  

You can make up to **10 guesses**.  
After each guess the `Master` object tells you how many characters match the secret word **at the same positions** (an integer `0 … 6`).  
If you guess the secret word, the game ends successfully; otherwise you have to keep guessing until you hit the secret word or run out of attempts.

Your job is to write a function

```java
void findSecretWord(String[] wordlist, Master master)
```

that makes the sequence of guesses and guarantees a win within the allowed number of attempts.

---

## 2.  What the interviewer is really asking

1. **Can you model the interaction as a search problem?**  
   – Each guess partitions the current candidate set according to the feedback value.  
2. **Which guess should you pick next?**  
   – We want to reduce the candidate set as quickly as possible (maximizing information gain).  
3. **What trade‑offs are you willing to make?**  
   – The optimal (minimax) strategy is expensive; a practical heuristic often suffices.  

Explain that we’re solving an *imperfect‑information game* with the objective of minimizing the worst‑case number of guesses.

---

## 3.  High‑level strategy

1. **Start with a “pivot” guess**  
   – Pick any word (random or deterministic).  
   – Call `master.guess(pivot)` → get `m` matches.

2. **Filter the list**  
   – Keep only words that also give exactly `m` matches with the pivot.  
   – All other words are impossible.

3. **Iterate**  
   – If the filtered list has only one word left, guess it.  
   – Otherwise, pick another word from the filtered list (often randomly).  
   – Repeat the filtering step using the new guess.

4. **Stop**  
   – Either you hit 6 matches (found the secret) or you’ve used all 10 guesses.

---

## 4.  Why this works

- **Feedback is exact** – If a word yields `k` matches, any candidate must yield the same `k`.  
- **Filtering shrinks the space exponentially** – In a random distribution, each guess eliminates roughly a constant fraction of the remaining words.  
- **Random or deterministic pivot** – Both are fine; random prevents pathological worst‑case constructions, deterministic (e.g. similarity‑based minimax) guarantees ≤ 10 guesses on average.

---

## 5.  Practical Algorithms

### 5.1  Random/Greedy Filtering (simple)

```java
class Solution {
    int match(String a, String b) { … }      // count same‑position chars

    public void findSecretWord(String[] words, Master master) {
        List<String> candidates = new ArrayList<>(Arrays.asList(words));

        for (int i = 0; i < 10 && !candidates.isEmpty(); i++) {
            // pick the i‑th word (or random from the list)
            String guess = candidates.get(0);          // deterministic
            int matches = master.guess(guess);
            if (matches == 6) return;                  // success

            // filter
            List<String> next = new ArrayList<>();
            for (String w : candidates) {
                if (w.equals(guess)) continue;
                if (match(guess, w) == matches) next.add(w);
            }
            candidates = next;
        }
    }
}
```

*Complexity:*  
- `match()` is `O(6)` → constant.  
- Each iteration scans all remaining words → `O(n)` per round.  
- In the worst case `O(n²)` but in practice the list shrinks quickly.

### 5.2  Similarity‑based Greedy (better for random data)

1. **Pre‑compute similarity score**  
   - For each word count how many other words share at least one same character at the same position.

2. **Sort the wordlist descending by this score**  
   - The most “popular” word will, on a `0` match feedback, eliminate many candidates.

3. **Guess in that order, filtering after every guess**  
   - Same filtering step as above.

*Why this helps:*  
When you expect most guesses to return `0` matches (≈80 % chance), picking a word that matches many others in at least one position removes a large chunk of possibilities if the feedback is `0`.

---

## 6.  Edge Cases & Discussion

| Scenario | What Happens | Why it’s OK |
|----------|--------------|------------|
| All words are completely distinct (e.g., `"abcdef", "ghijkl", …") | After the first guess only the exact word remains | One guess is enough |
| Worst‑case adversarial list | Random pivot might still succeed within 10 guesses | Even though the deterministic similarity approach may fail, the random filtering guarantees success in 10 attempts because the worst case size ≤ 10 |
| `wordlist.length` > 10 000 | Filtering still `O(n)` each round | n is bounded by the problem constraints |

---

## 7.  Complexity Summary

- **Time:**  
  `O(n² * 6)` worst‑case, but average case is `O(n log n)` due to exponential shrinkage.
- **Space:**  
  `O(n)` for the list of candidates.

---

## 8.  How to present this to an interviewer

1. **Clarify the problem:**  
   “We’re playing an interactive game; each guess gives us the number of correct positions.”

2. **Outline the core idea:**  
   “Filter the candidate set using the exact match feedback; any guess that does not match eliminates a large number of words.”

3. **Explain the algorithmic choice:**  
   - “I’ll pick the first word (or a random one) as my pivot, query the Master, and then keep only those words that produce the same feedback.”  
   - “I repeat until I find the secret or the list is down to one.”

4. **Discuss time/space trade‑offs**  
   “We scan the list every round, so it’s `O(n)` per guess. Because the list shrinks quickly, this is acceptable.”

5. **Mention extensions**  
   - “If we want to be more clever, we could compute a similarity score and always choose the most ‘informative’ word.”  
   - “That brings in minimax or entropy‑based ideas, but the simple greedy solution meets the 10‑guess limit.”

6. **Show code snippet**  
   “Here’s a concise implementation that follows the steps above.”

---

### Final Note

The core of the solution is **iterative filtering** based on exact‑position matches.  
Whether you choose a random pivot, the first word, or a similarity‑weighted pivot, the idea remains the same: each guess reduces the search space, and after a handful of rounds you will always be able to hit the secret word within the allowed attempts.