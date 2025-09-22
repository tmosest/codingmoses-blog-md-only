---
title: LeetCode 2684. Maximum Number of Moves in a Grid - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ```dart
import 'dart:math';

void main() {}

class Solution {
  int maxMoves(List<List<int>> grid) {
    final int m = grid.length;
    final int n = grid[0].length;
    final List<List<int>> dp = List.generate(m, (_) => List.filled(n, 0));

    for (int col = n - 2; col >= 0; col--) {
      for (int row = 0; row < m; row++) {
        if (row > 0 && grid[row][col] < grid[row - 1][col + 1]) {
          dp[row][col] = max(dp[row][col], dp[row - 1][col + 1] + 1);
        }
        if (grid[row][col] < grid[row][col + 1]) {
          dp[row][col] = max(dp[row][col], dp[row][col + 1] + 1);
        }
        if (row < m - 1 && grid[row][col] < grid[row + 1][col + 1]) {
          dp[row][col] = max(dp[row][col], dp[row + 1][col + 1] + 1);
        }
      }
    }

    int result = 0;
    for (int row = 0; row < m; row++) {
      result = max(result, dp[row][0]);
    }
    return result;
  }
}
```