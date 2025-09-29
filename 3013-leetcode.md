---
title: LeetCode 3013. Divide an Array Into Subarrays With Minimum Cost II - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        #include <bits/stdc++.h>
using namespace std;

long long solve(const vector<long long>& a, long long k, long long d) {
    int n = (int)a.size();
    if (k <= 1) return a[0];              // only one subarray needed
    int need = (int)k - 1;                // number of subarrays to choose
    long long best = LLONG_MAX;
    long long sumSel = 0;                 // current sum of selected elements

    set<pair<long long,int>> unused;      // elements not yet in the current window
    set<pair<long long,int>> cur;         // current window elements (maintained sorted)

    int r = 0;                            // last index that has been added to unused
    for (int l = 1; l <= n-1; ++l) {
        // add index l if it has not been added yet
        if (r < l) {
            while (r < l) {
                ++r;
                unused.insert({a[r], r});
            }
        }

        int target = (int)min((long long)l + d, (long long)n - 1);
        while (r < target) {
            ++r;
            unused.insert({a[r], r});
        }

        // move smallest elements from unused to cur until cur has 'need' elements
        while ((int)cur.size() < need && !unused.empty()) {
            auto it = unused.begin();              // smallest element
            cur.insert(*it);
            sumSel += it->first;
            unused.erase(it);
        }

        // keep the smallest 'need' elements in cur
        while (!cur.empty() && !unused.empty() &&
               cur.rbegin()->first > unused.begin()->first) {
            auto itCur = prev(cur.end());
            sumSel -= itCur->first;
            unused.insert(*itCur);
            cur.erase(itCur);

            auto itUn = unused.begin();
            cur.insert(*itUn);
            sumSel += itUn->first;
            unused.erase(itUn);
        }

        if ((int)cur.size() == need)
            best = min(best, sumSel);

        // remove element at position l before next iteration
        auto itCurL = cur.find({a[l], l});
        if (itCurL != cur.end()) {
            cur.erase(itCurL);
            sumSel -= a[l];
        } else {
            unused.erase({a[l], l});
        }
    }

    return best + a[0];
}
