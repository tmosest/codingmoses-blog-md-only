---
title: LeetCode 2601. Prime Subtraction Operation - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        import typing
from typing import List

class Solution:
    def __init__(self):
        self.primes = self._sieve(1000)

    def _sieve(self, n: int) -> List[int]:
        is_prime = [True] * (n + 1)
        is_prime[0] = is_prime[1] = False
        for i in range(2, int(n ** 0.5) + 1):
            if is_prime[i]:
                for j in range(i * i, n + 1, i):
                    is_prime[j] = False
        return [i for i, v in enumerate(is_prime) if v]

    def primeSubOperation(self, nums: List[int]) -> bool:
        for i in range(len(nums) - 2, -1, -1):
            if nums[i] >= nums[i + 1]:
                L = nums[i] - (nums[i + 1] - 1)
                found = False
                for p in reversed(self.primes):
                    if p > nums[i]:
                        continue
                    if p >= L:
                        nums[i] -= p
                        found = True
                        break
                if not found:
                    return False
        return True