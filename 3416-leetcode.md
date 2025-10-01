---
title: LeetCode 3416. Subsequences with a Unique Middle Mode II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        For a random shuffle of the nine distinct numbers \(0,1,\dots ,8\) each pair of distinct elements occurs in **exactly two** possible relative orders – the “right” order and the “wrong” order – each with probability \(1/2\).

In bubble‑sort (or any algorithm that swaps adjacent out‑of‑order pairs) a pair of elements is swapped **once** if and only if it is in the wrong order; otherwise it is never swapped.  
Hence for any particular pair \((a,b)\) with \(a\neq b\)

\[
\mathbb{E}[\text{# swaps between }a\text{ and }b]
= 1\;\times\;P(a\text{ and }b\text{ are inverted})
+ 0\;\times\;P(\text{no inversion})
= \frac{1}{2}.
\]

So the expected number of swaps between **any two adjacent distinct numbers** in a shuffled array of \(0\) to \(8\) is **\(0.5\)** (one swap with probability \(1/2\), none otherwise).

---

If you want the expected total number of adjacent swaps required to sort the whole array, note that there are  
\[
\binom{9}{2}=36
\]
different pairs of elements, each inverted with probability \(1/2\).  
Therefore

\[
\mathbb{E}[\text{total # swaps}] = 36 \times \frac12 = 18,
\]

which is the familiar result \(n(n-1)/4\) for the expected number of inversions in a random permutation of \(n\) distinct elements.