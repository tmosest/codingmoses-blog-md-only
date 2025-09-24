---
title: LeetCode 2458. Height of Binary Tree After Subtree Removal Queries - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ```java
class Solution {
    private int[] depth, left, right;
    private int[] leafHeights;
    private int leafCount;
    private static final int MAX_LEAF = 50000;
    private static final int MAX_NODES = 100001;

    public int[] treeQueries(TreeNode root, int[] queries) {
        depth = new int[MAX_NODES];
        left = new int[MAX_NODES];
        right = new int[MAX_NODES];
        leafHeights = new int[MAX_LEAF];
        leafCount = 0;

        dfs(root, 0);

        int n = leafCount;
        int[] prefMax = new int[n];
        int[] suffMax = new int[n];

        for (int i = 1; i < n; i++) {
            prefMax[i] = Math.max(prefMax[i - 1], leafHeights[i - 1]);
            suffMax[n - i - 1] = Math.max(suffMax[n - i], leafHeights[n - i]);
        }

        int[] res = new int[queries.length];
        for (int i = 0; i < queries.length; i++) {
            int q = queries[i];
            int maxLeft = prefMax[left[q]];
            int maxRight = suffMax[right[q]];
            int maxOther = Math.max(maxLeft, maxRight);
            res[i] = Math.max(maxOther, depth[q] - 1);
        }
        return res;
    }

    private void dfs(TreeNode node, int d) {
        depth[node.val] = d;
        if (node.left == null && node.right == null) {
            leafHeights[leafCount] = d;
            left[node.val] = right[node.val] = leafCount;
            leafCount++;
            return;
        }
        left[node.val] = leafCount;
        if (node.left != null) dfs(node.left, d + 1);
        if (node.right != null) dfs(node.right, d + 1);
        right[node.val] = leafCount - 1;
    }
}
```