---
title: LeetCode 555. Split Concatenated Strings - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Answer**

The “rotational‐concatenation” problem is in fact a classic string‑manipulation problem – the **minimum (lexicographically smallest) rotation** (sometimes called the *least cyclic shift* or *minimal cyclic shift*) problem.  

The standard way to solve it is to first join all the pieces into one long string (for example “BABC” for the list `['B','AB','C']`).  Then you run a linear‑time algorithm such as **Booth’s algorithm** (or use a suffix‑array/rolling‑hash approach) to find the index of the smallest lexicographic rotation of that concatenated string.  The rotation starting at that index is the unique “valid” ordering you are looking for.

If, for some reason, you only want to consider rotations that start on a word boundary (so the words are not split), you simply test each of the N possible rotations (each word becomes the first piece) and pick the lexicographically smallest.  This is trivial to do in O(N·L) time (or with a Booth‑style algorithm restricted to those N start positions). 

In short, the problem is the **minimal lexicographic rotation** (or “least cyclic shift”) problem, and it can be solved in linear time by Booth’s algorithm (or equivalently by string doubling with a suffix‑array, suffix‑tree, or even brute‑force comparison for a small N).