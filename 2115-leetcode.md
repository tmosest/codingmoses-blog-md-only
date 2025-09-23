---
title: LeetCode 2115. Find All Possible Recipes from Given Supplies - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Solution Explanation**

The problem is a classic *dependency graph* problem.  
Each ingredient or recipe can be considered a node.  
If a recipe needs an ingredient we draw an arrow **from the ingredient to the recipe**.

```
     carrot   ->  soup
      salt    ->  soup
     soup    ->  stew
```

The only “free” nodes are the given supplies – we can think of them as already prepared
ingredients.  
A recipe can be cooked as soon as **all** of its incoming edges (ingredients) are available.
After a recipe is cooked it can be used as a supply for other recipes.

This is exactly what a *topological sort* (Kahn’s algorithm) does:  
start with all free nodes (supplies), repeatedly remove a node, and
decrease the indegree of its outgoing neighbours.  
Whenever a neighbour’s indegree becomes zero we know that all of its ingredients
are now available – the recipe can be cooked.

The algorithm naturally works in *any* order in which the prerequisites are met, which
is all the problem asks for.



--------------------------------------------------------------------

#### Algorithm
```
1.  Build two maps:
        indegree[recipe] = number of ingredients needed
        graph[ingredient] = list of recipes that require this ingredient

2.  Put every supply into a queue.
    Also keep a set 'available' that initially contains all supplies.

3.  While the queue is not empty
        cur = queue.pop()
        // If cur is a recipe we just made, it is already in 'available'.
        For every recipe next that depends on cur
                indegree[next]--
                if indegree[next] == 0
                        // all its ingredients are available
                        result.push_back(next)
                        queue.push(next)   // this recipe becomes a new supply
                        available.insert(next)

4.  The vector 'result' now contains all recipes that can be cooked.
```

--------------------------------------------------------------------

#### Correctness Proof  

We prove that the algorithm returns exactly the set of recipes that can be cooked.

---

##### Lemma 1  
When a recipe `r` is inserted into the queue (and consequently into the result vector),
all its required ingredients are available at that moment.

**Proof.**  
`r` is inserted only when its `indegree[r]` becomes zero.
`indegree[r]` is decreased only when one of its required ingredients (or a recipe that
uses it) is removed from the queue.
Thus, for every required ingredient `x` of `r` there was a moment when `x` was removed
from the queue and `indegree[r]` was decremented by one.  
Since the final indegree is zero, each required ingredient was removed from the queue
exactly once, meaning it was available when `r` was processed. ∎



##### Lemma 2  
If a recipe `r` can be cooked using the given supplies, then the algorithm will
insert `r` into the queue.

**Proof.**  
We use induction over the length of the shortest derivation of `r`.

*Base:*  
If `r` uses only original supplies, all its ingredients are initially in the queue.
When the algorithm processes the first of those supplies, it will decrement
`indegree[r]` accordingly.  
After the last required supply has been processed, `indegree[r]` becomes zero and
`r` is inserted.

*Induction step:*  
Assume every recipe that can be cooked in at most `k` steps is inserted.  
Let `r` be a recipe that requires a recipe `p` that is itself cookable in `k` steps.
By the induction hypothesis, `p` will be inserted into the queue.
When `p` is processed, `indegree[r]` is decremented.
All other required ingredients of `r` are either original supplies or recipes
cookable in at most `k` steps, thus also inserted before `r` is considered.
Consequently, after the last such ingredient is processed, `indegree[r]` becomes zero
and `r` is inserted. ∎



##### Lemma 3  
No recipe that cannot be cooked is ever inserted into the queue.

**Proof.**  
A recipe is inserted only when its indegree reaches zero (Lemma&nbsp;1).
Indegree zero means all required ingredients have already been removed from the queue,
i.e. they are available.  
If a recipe were not cookable, at least one required ingredient is never available,
therefore its indegree can never become zero, and the recipe is never inserted. ∎



##### Theorem  
The output vector `result` contains exactly all recipes that can be cooked from the
given supplies.

**Proof.**  

*Soundness* – every recipe in `result` can be cooked:  
By Lemma&nbsp;1, each inserted recipe has all its ingredients available, so it is
cookable.

*Completeness* – every cookable recipe appears in `result`:  
By Lemma&nbsp;2 each cookable recipe will be inserted into the queue, and
by construction the algorithm pushes every inserted recipe into `result`. ∎



--------------------------------------------------------------------

#### Complexity Analysis

Let  

* `n` – number of recipes  
* `m` – total number of ingredient occurrences (sum of all ingredient list lengths)  

Building the two maps is `O(n + m)`.  
Each ingredient removal from the queue triggers a constant‑time processing of all
outgoing edges, which overall touches every edge once – `O(m)`.  
The queue operations themselves are `O(n + m)` as well.  
Hence the total running time is `O(n + m)` and the memory consumption is
`O(n + m)`.



--------------------------------------------------------------------

#### Reference Implementation (C++)

```cpp
#include <bits/stdc++.h>
using namespace std;

class Solution {
public:
    vector<string> findAllRecipes(vector<string>& recipes,
                                  vector<vector<string>>& ingredients,
                                  vector<string>& supplies) {
        unordered_map<string, vector<string>> graph;   // ingredient -> recipes that need it
        unordered_map<string, int> indegree;           // recipe -> number of missing ingredients
        unordered_set<string> available;              // all ingredients that are currently free
        vector<string> result;

        // 1. Build the graph and indegree map
        for (size_t i = 0; i < recipes.size(); ++i) {
            const string& recipe = recipes[i];
            indegree[recipe] = ingredients[i].size();
            for (const string& ing : ingredients[i]) {
                graph[ing].push_back(recipe);
            }
        }

        // 2. Queue all original supplies
        queue<string> q;
        for (const string& s : supplies) {
            if (available.insert(s).second) {   // insert only once
                q.push(s);
            }
        }

        // 3. Kahn's algorithm
        while (!q.empty()) {
            const string cur = q.front(); q.pop();

            // For each recipe that depends on 'cur', reduce its indegree
            if (graph.find(cur) != graph.end()) {
                for (const string& nxt : graph[cur]) {
                    --indegree[nxt];
                    if (indegree[nxt] == 0) {
                        // all ingredients of 'nxt' are now available
                        result.push_back(nxt);
                        available.insert(nxt);
                        q.push(nxt);          // 'nxt' becomes a new supply
                    }
                }
            }
        }

        return result;
    }
};
```

*The code follows the algorithm exactly;*  
comments inside the implementation mirror the steps described above.

--------------------------------------------------------------------

#### Alternative Implementations
If you prefer a DFS‑based solution or the brute‑force BFS approach, those are also
available, but the Kahn‑based method is the most efficient (linear time) and is
exactly what the problem expects.