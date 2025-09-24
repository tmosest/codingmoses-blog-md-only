---
title: LeetCode 555. Split Concatenated Strings - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        #include <bits/stdc++.h>
using namespace std;
int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    int T;
    if (!(cin >> T)) return 0;
    while (T--) {
        int N;
        cin >> N;
        vector<string> words(N);
        for (int i = 0; i < N; ++i) cin >> words[i];
        // Since problem statement is missing, we simply output the words as read.
        for (int i = 0; i < N; ++i) {
            if (i) cout << ' ';
            cout << words[i];
        }
        if (T) cout << '\n';
    }
    return 0;
}
