---
title: LeetCode 1257. Smallest Common Region - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 📚 1257 – Smallest Common Region  
> *“Find the smallest common ancestor of two nodes in a hierarchy”*  
> **Languages**: Java | Python | C++  
> **SEO Keywords**: Smallest Common Region, LeetCode 1257, LCA in a tree, Tree traversal, Java interview question, Python interview problem, C++ interview coding

---

## 1️⃣ Problem Overview

We’re given a hierarchy of geographic regions.  
Each input list `regions[i]` is of the form  

```
[ parent, child1, child2, … ]
```

* `parent` **directly** contains all `child*`.  
* The relationship is transitive – if *A* contains *B* and *B* contains *C*, then *A* indirectly contains *C*.

Given two region names (`region1`, `region2`) we must return **the smallest region that contains both**.  
It is guaranteed that such a region exists (the root of the tree).

> Example  
> `region1 = "Quebec"`, `region2 = "New York"` → **“North America”**

---

## 2️⃣ The Core Insight

The hierarchy is a **rooted tree** where each node has *exactly one* parent.  
Finding the smallest common region is equivalent to finding the **Lowest Common Ancestor (LCA)** of the two nodes.

Two classic ways to solve LCA problems:

| Approach | Time | Space | When to use |
|----------|------|-------|-------------|
| **Parent‑Map + Set** (our solution) | `O(N)` to build map + `O(H)` to traverse each node’s ancestors | `O(N)` for map, `O(H)` for set | Simple, no extra tree structure needed, works for any `H` ≤ `N` |
| **Binary Lifting / Euler Tour + RMQ** | `O(log N)` per query after `O(N log N)` preprocessing | `O(N log N)` | Needed when many queries on a large static tree |

Because the problem asks for a single query, the first approach is the most natural and easiest to implement in every language.

---

## 3️⃣ Algorithm (Parent‑Map + Set)

1. **Build a child → parent map**  
   For each list `L`:  
   * `parent = L[0]`  
   * For every `child` in `L[1 …]`, map `child → parent`.

2. **Collect ancestors of `region1`**  
   Walk up from `region1` to the root, inserting each visited node into a hash‑set.

3. **Find first common ancestor**  
   Walk up from `region2`.  
   The first node that appears in the set is the smallest common region.

Because the tree height is at most `N`, each walk takes `O(H)` time.

---

## 4️⃣ Complexity

| Step | Time | Space |
|------|------|-------|
| Build map | `O(N)` | `O(N)` |
| Walk `region1` | `O(H)` | `O(H)` |
| Walk `region2` | `O(H)` | — |
| **Total** | `O(N)` | `O(N)` |

`N` = total number of regions (`≤ 10^4`), `H` = tree height (`≤ N`).

---

## 5️⃣ Implementation

Below are clean, production‑ready implementations for **Java, Python, and C++**.  
All three follow the same algorithmic steps and use the same naming conventions.

> ⚠️ **Tip** – Always read input into the correct data structures (e.g., `List<List<String>>` in Java, `List[List[str]]` in Python, `vector<vector<string>>` in C++).  
> 📌 **Edge Cases** – The root may appear as a child in a different list? No – the problem guarantees a unique parent per child, so our map construction is safe.

### 5.1 Java

```java
import java.util.*;

public class Solution {
    /**
     * Finds the smallest common region that contains both region1 and region2.
     *
     * @param regions  List of region lists. Each list's first element is the parent,
     *                 the rest are its direct children.
     * @param region1  The first region name.
     * @param region2  The second region name.
     * @return The name of the smallest common region.
     */
    public String findSmallestRegion(List<List<String>> regions,
                                     String region1, String region2) {
        // 1. Build child → parent map
        Map<String, String> childParent = new HashMap<>();
        for (List<String> list : regions) {
            String parent = list.get(0);
            for (int i = 1; i < list.size(); i++) {
                childParent.put(list.get(i), parent);
            }
        }

        // 2. Ancestors of region1
        Set<String> ancestors = new HashSet<>();
        while (region1 != null) {
            ancestors.add(region1);
            region1 = childParent.get(region1);
        }

        // 3. Walk up from region2 to find first common ancestor
        while (region2 != null) {
            if (ancestors.contains(region2)) {
                return region2;
            }
            region2 = childParent.get(region2);
        }

        // Guaranteed by the problem that a common region exists.
        return null;
    }

    // Example usage / test
    public static void main(String[] args) {
        Solution sol = new Solution();
        List<List<String>> regions = Arrays.asList(
            Arrays.asList("Earth","North America","South America"),
            Arrays.asList("North America","United States","Canada"),
            Arrays.asList("United States","New York","Boston"),
            Arrays.asList("Canada","Ontario","Quebec"),
            Arrays.asList("South America","Brazil")
        );
        System.out.println(sol.findSmallestRegion(regions, "Quebec", "New York")); // North America
        System.out.println(sol.findSmallestRegion(regions, "Canada", "South America")); // Earth
    }
}
```

### 5.2 Python

```python
from typing import List, Dict, Set

class Solution:
    def findSmallestRegion(
        self,
        regions: List[List[str]],
        region1: str,
        region2: str
    ) -> str:
        # 1. child -> parent map
        child_parent: Dict[str, str] = {}
        for lst in regions:
            parent = lst[0]
            for child in lst[1:]:
                child_parent[child] = parent

        # 2. Ancestors of region1
        ancestors: Set[str] = set()
        while region1 is not None:
            ancestors.add(region1)
            region1 = child_parent.get(region1)

        # 3. Walk up from region2
        while region2 is not None:
            if region2 in ancestors:
                return region2
            region2 = child_parent.get(region2)

        return ""  # Unreachable – problem guarantees a solution


# Example test
if __name__ == "__main__":
    sol = Solution()
    regions = [
        ["Earth","North America","South America"],
        ["North America","United States","Canada"],
        ["United States","New York","Boston"],
        ["Canada","Ontario","Quebec"],
        ["South America","Brazil"]
    ]
    print(sol.findSmallestRegion(regions, "Quebec", "New York"))      # North America
    print(sol.findSmallestRegion(regions, "Canada", "South America")) # Earth
```

### 5.3 C++

```cpp
#include <iostream>
#include <vector>
#include <unordered_map>
#include <unordered_set>
#include <string>

class Solution {
public:
    std::string findSmallestRegion(
        const std::vector<std::vector<std::string>>& regions,
        std::string region1, std::string region2) {

        // 1. child → parent map
        std::unordered_map<std::string, std::string> childParent;
        for (const auto& lst : regions) {
            const std::string& parent = lst[0];
            for (size_t i = 1; i < lst.size(); ++i) {
                childParent[lst[i]] = parent;
            }
        }

        // 2. ancestors of region1
        std::unordered_set<std::string> ancestors;
        while (!region1.empty()) {
            ancestors.insert(region1);
            auto it = childParent.find(region1);
            region1 = (it == childParent.end()) ? "" : it->second;
        }

        // 3. walk up from region2
        while (!region2.empty()) {
            if (ancestors.count(region2)) return region2;
            auto it = childParent.find(region2);
            region2 = (it == childParent.end()) ? "" : it->second;
        }

        return ""; // Should never hit here
    }
};

int main() {
    Solution sol;
    std::vector<std::vector<std::string>> regions = {
        {"Earth","North America","South America"},
        {"North America","United States","Canada"},
        {"United States","New York","Boston"},
        {"Canada","Ontario","Quebec"},
        {"South America","Brazil"}
    };

    std::cout << sol.findSmallestRegion(regions, "Quebec", "New York") << '\n';   // North America
    std::cout << sol.findSmallestRegion(regions, "Canada", "South America") << '\n'; // Earth
}
```

---

## 6️⃣ “The Good, The Bad, The Ugly”

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Good** | • 1‑pass construction of a map – O(N) time & space.  <br>• Works for any tree shape, no special assumptions.  <br>• Code is short, easy to audit. | | |
| **Bad** | • Requires `O(N)` extra memory for the map – not ideal for gigantic trees.  <br>• For many repeated queries, we pay `O(N)` map build each time. | | |
| **Ugly** | • If the input lists are unsorted or contain duplicates, the map may overwrite parents.  <br>• The algorithm does *not* detect cycles – assumes a valid tree as per constraints.  <br>• In languages like Python, recursion depth is irrelevant here, but a stack‑based traversal is simpler. | | |

**What interviewers really want**  
- Correctness for all edge cases (root only, deep hierarchies).  
- Clear reasoning about why the algorithm finds the *smallest* common region (the first common ancestor walking up).  
- Time/space trade‑off discussion – mention that a binary lifting solution would reduce query time at the cost of more memory and preprocessing.

---

## 7️⃣ Interview Tips

1. **Clarify the query count** – ask whether multiple queries might be needed.  
2. **State the constraints** – confirm that each child has only one parent and no cycles.  
3. **Show the “first common ancestor” intuition** – many candidates overlook that the walk must start from `region1` and use a set.  
4. **Explain the complexity** – highlight the linear preprocessing and linear ancestor traversal.  
5. **Ask about preprocessing** – a good follow‑up: “If we had 10⁵ queries, what would you change?” – answer with binary lifting / RMQ.

---

## 8️⃣ Final Thought

The “parent‑map + set” solution is a textbook example of **map‑based ancestor tracking**.  
It blends readability, simplicity, and solid performance for a single query – exactly what this LeetCode problem requires.  
With the three language implementations above, you’re ready to tackle the problem in any interview or production setting.

Happy coding! 🚀

--- 

*If you found this guide helpful, feel free to ⭐️ on GitHub, share it with friends, or comment below if you have questions about the “ugly” edge cases.*