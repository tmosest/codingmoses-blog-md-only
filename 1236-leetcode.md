---
title: LeetCode 1236. Web Crawler - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        # 🚀 Mastering Leetcode 1236 – **Web Crawler**  
**Java | Python | C++ solutions + a full “Good / Bad / Ugly” blog post**  

> *“If you want to land a software‑engineering job, you need to ace the classic interview problems. Leetcode 1236 (Web Crawler) is a gold‑mine that tests graph traversal, string parsing, and careful edge‑case handling.”*

---

## 📄 Problem Recap

> **Goal** – Starting from `startUrl`, crawl every URL that lies under the *same hostname* (e.g. `http://example.org`) using the provided `HtmlParser`.  
> **Constraints**  
> * No duplicate crawling (each URL visited only once).  
> * Ignore URLs that point to a different hostname.  
> * Treat URLs with/without a trailing slash as distinct.  
> * All URLs use the `http` protocol and no custom ports.

> **Output** – A list of all reachable URLs (any order).  

> **Time** – O(V + E)  
> **Space** – O(V)

---

## 🔧 Solution Overview

The graph is implicit: every URL is a node, and the edges are the hyperlinks returned by `htmlParser.getUrls(url)`.  
A classic **Breadth‑First Search (BFS)** (or Depth‑First Search) is all we need.

1. **Extract the hostname** of `startUrl`.  
   `hostname = url[7: url.find('/', 7)]` (the part after `"http://"` and before the first `/` after it).  
2. **Queue** – for BFS; **Stack** – for DFS.  
3. **Visited set** – to avoid revisiting URLs.  
4. While the queue/stack is not empty:  
   * Pop a URL.  
   * Ask `htmlParser.getUrls(url)` for neighbours.  
   * For each neighbour that shares the hostname **and** hasn’t been visited, add it to the visited set and push it onto the queue/stack.  
5. Return the visited set as a list.

---

## 🛠️ Code – Java

```java
import java.util.*;

class Solution {
    /** Interface given by the platform (do not implement) */
    interface HtmlParser {
        List<String> getUrls(String url);
    }

    public List<String> crawl(String startUrl, HtmlParser htmlParser) {
        String host = getHostname(startUrl);

        Queue<String> q = new ArrayDeque<>();
        Set<String> visited = new HashSet<>();

        q.offer(startUrl);
        visited.add(startUrl);

        while (!q.isEmpty()) {
            String url = q.poll();
            for (String nxt : htmlParser.getUrls(url)) {
                if (nxt.startsWith(host) && visited.add(nxt)) {
                    q.offer(nxt);
                }
            }
        }
        return new ArrayList<>(visited);
    }

    private String getHostname(String url) {
        int idx = url.indexOf('/', 7);          // skip "http://"
        return idx == -1 ? url : url.substring(0, idx);
    }
}
```

---

## 🛠️ Code – Python

```python
from collections import deque
from typing import List

# The platform supplies this interface – do not implement it
class HtmlParser:
    def getUrls(self, url: str) -> List[str]:
        ...

class Solution:
    def crawl(self, startUrl: str, htmlParser: HtmlParser) -> List[str]:
        host = self._hostname(startUrl)

        q = deque([startUrl])
        visited = {startUrl}

        while q:
            url = q.popleft()
            for nxt in htmlParser.getUrls(url):
                if nxt.startswith(host) and nxt not in visited:
                    visited.add(nxt)
                    q.append(nxt)

        return list(visited)

    @staticmethod
    def _hostname(url: str) -> str:
        idx = url.find('/', 7)          # skip "http://"
        return url if idx == -1 else url[:idx]
```

---

## 🛠️ Code – C++

```cpp
#include <string>
#include <vector>
#include <queue>
#include <unordered_set>
using namespace std;

// The platform supplies this interface – do not implement
class HtmlParser {
public:
    virtual vector<string> getUrls(const string& url) = 0;
};

class Solution {
public:
    vector<string> crawl(string startUrl, HtmlParser& htmlParser) {
        string host = getHostname(startUrl);

        queue<string> q;
        unordered_set<string> visited;

        q.push(startUrl);
        visited.insert(startUrl);

        while (!q.empty()) {
            string url = q.front(); q.pop();
            vector<string> neigh = htmlParser.getUrls(url);
            for (const string& nxt : neigh) {
                if (nxt.compare(0, host.size(), host) == 0 &&
                    visited.insert(nxt).second) {   // inserted successfully
                    q.push(nxt);
                }
            }
        }
        return vector<string>(visited.begin(), visited.end());
    }

private:
    static string getHostname(const string& url) {
        size_t pos = url.find('/', 7);  // skip "http://"
        return pos == string::npos ? url : url.substr(0, pos);
    }
};
```

---

## 📚 The “Good, The Bad, and The Ugly” – Interview Insight

| Aspect | Good | Bad | Ugly |
|--------|------|-----|------|
| **Graph traversal choice** | BFS guarantees minimal depth; DFS is simpler to write recursively | DFS recursion may blow the stack for huge webs | None – both work; choose the one you’re comfortable explaining |
| **Hostname extraction** | Simple `startswith` check saves time | Hard to get right on the first try (off‑by‑one, missing protocol) | Forgetting to handle URLs without a trailing slash (e.g. `http://a.com` vs `http://a.com/`) leads to wrong answers |
| **Visited‑set handling** | `HashSet`/`unordered_set` prevents O(n) lookups | Need to insert *and* check if insertion succeeded | Mis‑using `insert` vs `find` in C++ can produce subtle bugs |
| **String matching** | `startsWith(host)` is O(1) | Using `contains(host)` may incorrectly match `http://evil.com` inside a different URL | Not escaping special characters or handling `https://` leads to false positives |
| **Recursion depth** | DFS recursion is elegant | Stack overflow on deep web pages | Recursion depth > 10⁴ → runtime crash |
| **Complexity** | O(V + E) – optimal for interview | None | Forgetting the hostname filter → O(V + E + X) where X is the number of cross‑host links |
| **Edge cases** | Trailing slash treated as distinct (explicit check) | URLs may contain query params; still same hostname – fine | `htmlParser.getUrls` returning the same URL repeatedly – still safe because of the visited set |

### Take‑away

*Always explain your design choices.*  
Tell the interviewer why you’re using BFS, how you’re guaranteeing no duplicates, and how you’re extracting the hostname safely.  
If you can also point out the pitfalls (e.g., the “ugly” part of string parsing) you’ll demonstrate maturity.

---

## 🔑 How to Use This in a Job Interview

1. **Show the skeleton** – start with the platform interface and the `Solution` class.  
2. **Talk through the hostname extraction** – explain why the index starts at 7 (`"http://"`).  
3. **Discuss data structures** – why a `queue` over a stack (if you choose BFS).  
4. **State complexity** – emphasize the O(V + E) behavior.  
5. **Mention pitfalls** – trailing slashes, duplicate URLs, recursion limits.  

> **Tip**: If the interviewer asks for a DFS version, swap the queue for a stack or a recursive helper. The core logic stays the same.

---

## 🎯 SEO‑Optimized Blog Post

> **Title**:  
> *“Mastering Leetcode 1236: Web Crawler – The Good, The Bad, and The Ugly”*  

> **Meta‑Keywords**:  
> `Leetcode 1236 Web Crawler`, `Java BFS Web Crawler`, `Python DFS Web Crawler`, `C++ Leetcode solutions`, `software engineering interview`, `coding interview tips`, `job interview coding problems`

### Article

> **Introduction**  
> The Web Crawler problem is a hidden gem in the Leetcode “Graph” bucket.  
> It forces you to juggle graph traversal with string manipulation – a classic interview combo.

> **What Makes It Great for Your Resume**  
> * Demonstrates mastery of BFS/DFS.  
> * Highlights attention to detail (hostname extraction, duplicate handling).  
> * Shows ability to write clean, reusable code in multiple languages.

> **Code Snippets**  
> • Java (BFS) – see section above  
> • Python (BFS) – see section above  
> • C++ (BFS) – see section above  

> **Complexity Analysis**  
> * **Time**: Each URL is processed once; each edge (hyperlink) examined once → O(V + E).  
> * **Space**: The queue/stack holds at most all reachable URLs → O(V).  

> **Common Interview Pitfalls**  
> 1. **Hostname parsing** – beginners often forget the offset of 7 for `"http://"` or mishandle URLs without a trailing `/`.  
> 2. **Visited set** – using `visited.contains` then `add` can double‑process a node if not careful.  
> 3. **Recursion depth** – a naïve DFS recursion can hit the stack limit on a very deep link chain.  

> **Pro‑Tip**  
> Write a helper `getHostname()` first. Test it on edge cases:  
> * `http://a.com` (no slash)  
> * `http://a.com/` (root)  
> * `http://a.com/path`  

> **Wrap‑Up**  
> Mastering Web Crawler equips you with a robust template for *any* graph‑traversal interview problem. Practice the BFS/DFS pattern, tweak the string‑matching logic for different domains, and you’ll be ready to ace the next coding round.  

> **Call‑to‑Action**  
> Want to see more Leetcode gold‑mines? Follow me on GitHub, LinkedIn, and Medium for bite‑sized interview solutions that land interviews!

---

## 📞 Ready to Land Your Next Job?

Drop me a line on LinkedIn if you’d like a deeper dive, a mock interview, or a personalized study plan.  
*Let’s turn Leetcode 1236 into your interview success story!*

---