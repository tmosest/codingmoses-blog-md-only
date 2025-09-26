---
title: LeetCode 2456. Most Popular Video Creator - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 1.  Problem Recap

> **2456 – Most Popular Video Creator**  
> *Medium*  

You’re given three arrays of length *n*:

| i | `creators[i]` | `ids[i]` | `views[i]` |
|---|---------------|----------|------------|
| 0 | creator of video *i* | id of video *i* | number of views of video *i* |

The *popularity* of a creator is the sum of the views of all of that creator’s videos.  
For every creator you also need to know the id of the **most‑viewed** video.  
If several videos tie for most views, pick the lexicographically smallest id.  
If several creators tie for the highest popularity, return all of them (order does not matter).

Return a 2‑D array `answer` where each element is `[creator, id_of_best_video]`.



--------------------------------------------------------------------

## 2.  Algorithm (One‑pass, O(n) time, O(n) space)

The solution is a single linear scan that keeps a *single* hash‑map per creator.

| Creator | `totalViews` | `maxVideoViews` | `bestId` |
|---------|--------------|-----------------|----------|

* `totalViews` – the running sum of all views for that creator.  
* `maxVideoViews` – the maximum view count seen for any single video of that creator.  
* `bestId` – the lexicographically smallest id that has `maxVideoViews`.

**Steps**

1. Initialise an empty map `mp` (`String → struct{ long total, int maxView, String bestId }`).
2. Iterate over the 3 arrays simultaneously.
   * Update `totalViews` (add current `views[i]`).
   * If `views[i] > maxVideoViews` → replace `maxVideoViews` and `bestId`.
   * If `views[i] == maxVideoViews` → keep the smaller id.
3. While iterating keep track of the global maximum popularity (`globalMax`).
4. After the scan, walk the map again and pick all creators whose `totalViews == globalMax`.  
   For each, output `[creator, bestId]`.

Because every operation on a map is **O(1)** on average, the overall complexity is:

* **Time** – `O(n)`  
* **Space** – `O(n)` (one entry per distinct creator)

The algorithm handles duplicate ids naturally because we treat each entry as a distinct video.



--------------------------------------------------------------------

## 3.  Reference Implementations

Below you’ll find clean, well‑commented solutions in the three requested languages.

---

### 3.1  Java (Java 17)

```java
import java.util.*;

public class Solution {
    // Helper class to hold creator statistics
    private static class Stat {
        long totalViews;      // sum of all views
        int  maxVideoViews;   // max views of a single video
        String bestId;        // lexicographically smallest id among maxVideoViews

        Stat(long views, String id) {
            this.totalViews = views;
            this.maxVideoViews = (int) views;
            this.bestId = id;
        }

        void update(long views, String id) {
            totalViews += views;

            if (views > maxVideoViews) {
                maxVideoViews = (int) views;
                bestId = id;
            } else if (views == maxVideoViews && id.compareTo(bestId) < 0) {
                bestId = id;
            }
        }
    }

    public List<List<String>> mostPopularCreator(String[] creators,
                                                 String[] ids,
                                                 int[] views) {
        Map<String, Stat> mp = new HashMap<>();
        long globalMax = 0;

        for (int i = 0; i < creators.length; i++) {
            String creator = creators[i];
            String id      = ids[i];
            long v         = views[i];

            mp.computeIfAbsent(creator,
                k -> new Stat(v, id))
              .update(v, id);

            globalMax = Math.max(globalMax, mp.get(creator).totalViews);
        }

        List<List<String>> ans = new ArrayList<>();
        for (var entry : mp.entrySet()) {
            if (entry.getValue().totalViews == globalMax) {
                ans.add(Arrays.asList(entry.getKey(), entry.getValue().bestId));
            }
        }
        return ans;
    }

    // Quick demo (optional)
    public static void main(String[] args) {
        Solution s = new Solution();
        String[] c = {"alice", "bob", "alice", "bob", "alice"};
        String[] id = {"1", "2", "3", "4", "5"};
        int[] v = {5, 3, 5, 2, 5};

        System.out.println(s.mostPopularCreator(c, id, v));
    }
}
```

---

### 3.2  Python (Python 3.10)

```python
from typing import List, Dict

class Stat:
    def __init__(self, views: int, vid_id: str):
        self.total_views = views
        self.max_video   = views
        self.best_id     = vid_id

    def update(self, views: int, vid_id: str) -> None:
        self.total_views += views
        if views > self.max_video:
            self.max_video = views
            self.best_id   = vid_id
        elif views == self.max_video and vid_id < self.best_id:
            self.best_id = vid_id

def mostPopularCreator(creators: List[str], ids: List[str], views: List[int]) -> List[List[str]]:
    mp: Dict[str, Stat] = {}
    global_max = 0

    for creator, vid_id, v in zip(creators, ids, views):
        if creator not in mp:
            mp[creator] = Stat(v, vid_id)
        else:
            mp[creator].update(v, vid_id)

        global_max = max(global_max, mp[creator].total_views)

    answer: List[List[str]] = []
    for creator, stat in mp.items():
        if stat.total_views == global_max:
            answer.append([creator, stat.best_id])

    return answer


# Demo (optional)
if __name__ == "__main__":
    creators = ["alice", "bob", "alice", "bob", "alice"]
    ids      = ["1", "2", "3", "4", "5"]
    views    = [5, 3, 5, 2, 5]
    print(mostPopularCreator(creators, ids, views))
```

---

### 3.3  C++ (C++20)

```cpp
#include <bits/stdc++.h>
using namespace std;

// Structure that stores all statistics for a creator
struct Stat {
    long long totalViews{0};      // sum of all views
    int maxVideoViews{0};         // maximum views of a single video
    string bestId;                // smallest id with maxVideoViews

    Stat(long long v, const string &id)
        : totalViews(v), maxVideoViews(static_cast<int>(v)), bestId(id) {}

    void update(long long v, const string &id) {
        totalViews += v;

        if (v > maxVideoViews) {
            maxVideoViews = static_cast<int>(v);
            bestId = id;
        } else if (v == maxVideoViews && id < bestId) {
            bestId = id;
        }
    }
};

class Solution {
public:
    vector<vector<string>> mostPopularCreator(vector<string>& creators,
                                              vector<string>& ids,
                                              vector<int>& views) {
        unordered_map<string, Stat> mp;
        long long globalMax = 0;

        for (size_t i = 0; i < creators.size(); ++i) {
            const string &creator = creators[i];
            const string &id      = ids[i];
            long long v           = views[i];

            if (!mp.count(creator))
                mp[creator] = Stat(v, id);
            else
                mp[creator].update(v, id);

            globalMax = max(globalMax, mp[creator].totalViews);
        }

        vector<vector<string>> ans;
        for (const auto &[creator, st] : mp) {
            if (st.totalViews == globalMax) {
                ans.push_back({creator, st.bestId});
            }
        }
        return ans;
    }
};
```

All three solutions run in *O(n)* time and use *O(n)* auxiliary space – the optimal bounds for this problem.



--------------------------------------------------------------------

## 4.  Good, Bad & Ugly – What Interviewers Actually Want

| Aspect | What’s Good | Where Things Go Wrong | “Ugly” Pitfall |
|--------|--------------|-----------------------|----------------|
| **Good** | • One hash‑map per creator → **O(n)** passes. <br>• Keeps *exactly* the data you need: total, max video, and best id. <br>• Handles duplicate ids automatically. | | |
| **Bad** | • Using 2–3 nested maps (one for popularity, another for per‑id stats) adds *O(k)* space (k = distinct ids). <br>• You risk a subtle bug: adding the same id’s views instead of taking the *maximum* of a single video. | This often shows up in contests: “I summed the same video’s views twice.” |
| **Ugly** | • When several videos tie, picking the smallest id requires a *lexicographic comparison* – a tiny but crucial detail. <br>• If you forget to track the global maximum popularity during the first pass, you’ll have to run a second pass anyway. | The classic “missing last test case” story – it’s usually a corner case around ties or empty input. |

### How to Avoid the Ugly

| Tactic | Why it matters |
|--------|----------------|
| **Single struct per creator** | Keeps the logic in one place – no accidental double‑counting. |
| **`computeIfAbsent` / `map.getOrDefault`** | Guarantees that the map entry is created only once. |
| **Always compare id strings** | `id.compareTo(bestId) < 0` guarantees the smallest id for ties. |
| **Track `globalMax` on the fly** | Avoids a second full pass if you only need the list of max‑pop creators. |

When you’re ready for the job‑interview, you can drop any of the reference codes into your solution repository, or even port it to another language (Rust, Go, etc.) – the same pattern applies.



--------------------------------------------------------------------

## 5.  Complexity Analysis

| Metric | Formula | Result |
|--------|---------|--------|
| **Time** | `O(n)` – single loop, constant‑time hash operations | **Linear** |
| **Space** | `O(m)` where *m* = number of distinct creators | **Linear** |

Because *m* ≤ *n*, the worst‑case space is `O(n)`.



--------------------------------------------------------------------

## 6.  Why This Problem Is a Must‑Know for Your Next Interview

* **Real‑world data** – Think of YouTube or TikTok: many creators, many videos, huge datasets.  
* **Map heavy** – Most senior developers need to be comfortable with hash‑tables, `unordered_map`, `HashMap`, etc.  
* **Tie‑breaking logic** – Interviewers love to test your ability to handle edge cases and lexical ordering.  
* **Multiple answer format** – Shows you can return collections of pairs, a common interview pattern.  

By mastering this problem you demonstrate:

* **Algorithmic efficiency** (linear time, constant‑space updates).  
* **Clean coding style** (single data structure, no nested loops).  
* **Attention to edge cases** (duplicate ids, ties, multiple creators).  
* **Interview readiness** – you can explain your reasoning on the whiteboard in under 5 minutes.

---

## 7.  SEO‑Friendly Summary

- **LeetCode 2456** – *Most Popular Video Creator*  
- Java solution (HashMap, O(n) time, O(n) space)  
- Python solution (dict, O(n) time, O(n) space)  
- C++ solution (unordered_map, O(n) time, O(n) space)  
- Interview prep – explain ties, tie‑breaking, data‑structure choice  
- Algorithmic complexity – time O(n), space O(n)  
- Code demo – fast, clean, single‑pass  

If you’re prepping for a software‑engineering interview, add this article to your study list. The pattern of **one map + global max** is a reusable trick that shows up in many “top‑k” or “group‑by” problems on LeetCode and beyond.



--------------------------------------------------------------------

## 8.  Take‑Away

1. **Use a single hash‑map per creator** – keeps the code short and eliminates accidental double‑counting.  
2. **Update statistics on the fly** – no second pass through the videos, only one pass through the creators after the scan.  
3. **Watch out for ties** – keep the lexicographically smallest id whenever you hit a view‑equal situation.  
4. **Test the edge cases** – empty arrays, one creator only, all creators tie, duplicate ids with same views, etc.  

Happy coding—and may your next interview go *stream‑lined* like a hit‑viral video! 🚀