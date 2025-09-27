---
title: LeetCode 2916. Subarrays Distinct Element Sum of Squares II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        #include <bits/stdc++.h>
using namespace std;

const long long MOD = 1000000007LL;

struct Fenwick {
    int n;
    vector<long long> bitA, bitB;
    Fenwick(int sz = 0) { init(sz); }
    void init(int sz) {
        n = sz + 2; // extra space for r+1 updates
        bitA.assign(n + 2, 0);
        bitB.assign(n + 2, 0);
    }
    void internalAdd(vector<long long> &bit, int idx, long long val) {
        val %= MOD;
        if (val < 0) val += MOD;
        while (idx <= n) {
            bit[idx] += val;
            if (bit[idx] >= MOD) bit[idx] -= MOD;
            idx += idx & -idx;
        }
    }
    void rangeAdd(int l, int r, long long val) { // 1-based indices inclusive
        internalAdd(bitA, l, val);
        internalAdd(bitA, r + 1, -val);
        internalAdd(bitB, l, val * (l - 1) % MOD);
        internalAdd(bitB, r + 1, -(val * r % MOD));
    }
    long long prefixSum(int k) { // sum of f[1..k] inclusive
        long long sumA = 0, sumB = 0;
        int idx = k;
        while (idx > 0) {
            sumA += bitA[idx];
            if (sumA >= MOD) sumA -= MOD;
            idx -= idx & -idx;
        }
        idx = k;
        while (idx > 0) {
            sumB += bitB[idx];
            if (sumB >= MOD) sumB -= MOD;
            idx -= idx & -idx;
        }
        long long res = (sumA * k) % MOD;
        res += sumB;
        if (res >= MOD) res -= MOD;
        return res;
    }
    long long rangeSum(int l, int r) { // sum of f[l..r] inclusive
        if (l > r) return 0;
        long long res = prefixSum(r) - prefixSum(l - 1);
        if (res < 0) res += MOD;
        return res;
    }
};

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);
    
    int n;
    if (!(cin >> n)) return 0;
    vector<int> a(n);
    for (int i = 0; i < n; ++i) cin >> a[i];
    
    const int MAX_VAL = 100000; // as per constraints
    vector<int> lastPos(MAX_VAL + 1, -1);
    
    Fenwick fw(n);
    long long ans = 0;
    long long dpPrev = 0; // dp for i-1
    
    for (int i = 0; i < n; ++i) {
        int val = a[i];
        int prevIdx = lastPos[val];
        int l, r;
        if (prevIdx == -1) {
            l = 1;
            r = i + 1; // positions are 1-based
        } else {
            l = prevIdx + 2; // because prevIdx is 0-based
            r = i + 1;
        }
        long long sum_f = fw.rangeSum(l, r);
        long long dp = (dpPrev + 2 * sum_f % MOD + (i - prevIdx)) % MOD;
        ans += dp;
        if (ans >= MOD) ans -= MOD;
        fw.rangeAdd(i + 1, i + 1, 1); // add 1 at position i+1
        lastPos[val] = i;
        dpPrev = dp;
    }
    
    cout << ans % MOD << "\n";
    return 0;
}
