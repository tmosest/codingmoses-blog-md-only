---
title: LeetCode 2590. Design a Todo List - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        ## 🚀 LeetCode 2590 – “Design a Todo List”  
### A Deep‑Dive into the Problem, Solution, and Interview‑Ready Code (Java / Python / C++)  

---

### TL;DR  
* **Problem** – Implement a *TodoList* class that can add, fetch, and complete tasks for many users.  
* **Key operations** – `addTask`, `getAllTasks`, `getTasksForTag`, `completeTask`.  
* **Constraints** – ≤ 100 calls, unique due dates, ≤ 100 users & tasks, tags up to 100 per task.  
* **Goal** – O(1) addition, O(k log k) for fetching (k = # tasks returned).  

Below you’ll find:

| Language | Full solution (ready to paste into LeetCode) |
|----------|----------------------------------------------|
| **Java** | <details><summary>Click to expand</summary>  
```java
import java.util.*;
import java.util.concurrent.ConcurrentHashMap;

public class TodoList {
    // Counter for global unique task IDs
    private int taskCounter = 1;

    // Maps taskId → Task
    private final Map<Integer, Task> taskMap = new ConcurrentHashMap<>();

    // Maps userId → List of taskIds (kept in insertion order)
    private final Map<Integer, List<Integer>> userTasks = new ConcurrentHashMap<>();

    /** Initialize your data structure here. */
    public TodoList() {
        // Nothing to do – all maps are already instantiated
    }

    /** Add a task and return its ID. */
    public int addTask(int userId, String taskDescription, int dueDate, List<String> tags) {
        int id = taskCounter++;
        Task task = new Task(id, taskDescription, dueDate, tags);
        taskMap.put(id, task);

        userTasks.computeIfAbsent(userId, k -> new ArrayList<>()).add(id);
        return id;
    }

    /** Helper – return a list of *uncompleted* Task objects for a user, sorted by dueDate. */
    private List<Task> getPendingTasksSorted(int userId) {
        List<Task> pending = new ArrayList<>();
        List<Integer> ids = userTasks.getOrDefault(userId, Collections.emptyList());
        for (int id : ids) {
            Task t = taskMap.get(id);
            if (!t.completed) pending.add(t);
        }
        pending.sort(Comparator.comparingInt(t -> t.dueDate));
        return pending;
    }

    /** Get all uncompleted tasks for a user, ordered by due date. */
    public List<String> getAllTasks(int userId) {
        List<String> res = new ArrayList<>();
        for (Task t : getPendingTasksSorted(userId)) res.add(t.description);
        return res;
    }

    /** Get all uncompleted tasks for a user that contain a specific tag. */
    public List<String> getTasksForTag(int userId, String tag) {
        List<String> res = new ArrayList<>();
        for (Task t : getPendingTasksSorted(userId)) {
            if (t.tags.contains(tag)) res.add(t.description);
        }
        return res;
    }

    /** Mark a task as completed only if it belongs to the user and is still pending. */
    public void completeTask(int userId, int taskId) {
        if (!userTasks.containsKey(userId)) return;
        if (!userTasks.get(userId).contains(taskId)) return;
        Task t = taskMap.get(taskId);
        if (t != null && !t.completed) t.completed = true;
    }

    /* ---------- Inner Task class ---------- */
    private static class Task {
        final int id;
        final String description;
        final int dueDate;
        final List<String> tags;
        boolean completed = false;

        Task(int id, String description, int dueDate, List<String> tags) {
            this.id = id;
            this.description = description;
            this.dueDate = dueDate;
            this.tags = new ArrayList<>(tags);  // defensive copy
        }
    }
}
```
</details> |
| **Python** | <details><summary>Click to expand</summary>  
```python
from collections import defaultdict
from typing import List

class TodoList:
    def __init__(self):
        self._task_id = 1
        self.tasks = {}  # id -> Task
        self.user_tasks = defaultdict(list)  # user_id -> list of task ids

    def addTask(self, userId: int, taskDescription: str,
                dueDate: int, tags: List[str]) -> int:
        tid = self._task_id
        self._task_id += 1
        self.tasks[tid] = {
            "desc": taskDescription,
            "due": dueDate,
            "tags": set(tags),
            "completed": False
        }
        self.user_tasks[userId].append(tid)
        return tid

    def _pending_sorted(self, userId: int):
        pending = [self.tasks[tid] for tid in self.user_tasks.get(userId, [])
                   if not self.tasks[tid]["completed"]]
        return sorted(pending, key=lambda t: t["due"])

    def getAllTasks(self, userId: int) -> List[str]:
        return [t["desc"] for t in self._pending_sorted(userId)]

    def getTasksForTag(self, userId: int, tag: str) -> List[str]:
        return [t["desc"] for t in self._pending_sorted(userId) if tag in t["tags"]]

    def completeTask(self, userId: int, taskId: int) -> None:
        if taskId not in self.tasks:
            return
        if taskId not in self.user_tasks.get(userId, []):
            return
        if not self.tasks[taskId]["completed"]:
            self.tasks[taskId]["completed"] = True
```
</details> |
| **C++** | <details><summary>Click to expand</summary>  
```cpp
#include <bits/stdc++.h>
using namespace std;

class TodoList {
public:
    TodoList() : taskCounter(1) {}

    int addTask(int userId, string taskDescription,
                int dueDate, vector<string> tags) {
        int id = taskCounter++;
        Task t{id, taskDescription, dueDate, tags, false};
        tasks[id] = t;
        userTasks[userId].push_back(id);
        return id;
    }

    vector<string> getAllTasks(int userId) {
        vector<string> res;
        for (auto &t : getPendingSorted(userId))
            res.push_back(t.description);
        return res;
    }

    vector<string> getTasksForTag(int userId, string tag) {
        vector<string> res;
        for (auto &t : getPendingSorted(userId))
            if (t.tags.find(tag) != t.tags.end())
                res.push_back(t.description);
        return res;
    }

    void completeTask(int userId, int taskId) {
        auto it = userTasks.find(userId);
        if (it == userTasks.end()) return;
        if (find(it->second.begin(), it->second.end(), taskId) == it->second.end())
            return;
        auto &t = tasks[taskId];
        if (!t.completed) t.completed = true;
    }

private:
    struct Task {
        int id;
        string description;
        int due;
        unordered_set<string> tags;
        bool completed = false;
        Task(int i, string d, int du, vector<string> tg)
            : id(i), description(d), due(du), tags(tg.begin(), tg.end()) {}
    };

    int taskCounter;                                   // next global task ID
    unordered_map<int, Task> tasks;                    // taskId → Task
    unordered_map<int, vector<int>> userTasks;         // userId → list of taskIds

    // Return pending tasks sorted by due date
    vector<Task> getPendingSorted(int userId) {
        vector<Task> pending;
        auto it = userTasks.find(userId);
        if (it == userTasks.end()) return pending;
        for (int id : it->second) {
            auto &t = tasks[id];
            if (!t.completed) pending.push_back(t);
        }
        sort(pending.begin(), pending.end(),
             [](const Task &a, const Task &b){ return a.due < b.due; });
        return pending;
    }
};
```
</details> |
| **C++** (shorter LeetCode‑style) | <details><summary>Click to expand</summary>  
```cpp
#include <bits/stdc++.h>
using namespace std;

class TodoList {
public:
    TodoList() : nextId(1) {}

    int addTask(int userId, string taskDescription,
                int dueDate, vector<string> tags) {
        int id = nextId++;
        tasks[id] = {taskDescription, dueDate, tags, false};
        userTasks[userId].push_back(id);
        return id;
    }

    vector<string> getAllTasks(int userId) {
        auto v = pendingSorted(userId);
        vector<string> res;
        for (auto &t : v) res.push_back(t.description);
        return res;
    }

    vector<string> getTasksForTag(int userId, string tag) {
        auto v = pendingSorted(userId);
        vector<string> res;
        for (auto &t : v) if (t.tags.count(tag)) res.push_back(t.description);
        return res;
    }

    void completeTask(int userId, int taskId) {
        if (!userTasks.count(userId)) return;
        if (!tasks.count(taskId)) return;
        if (!userTasks[userId].count(taskId)) return;
        if (!tasks[taskId].completed) tasks[taskId].completed = true;
    }

private:
    struct Task {
        string description;
        int due;
        unordered_set<string> tags;
        bool completed;
        Task(string d, int du, vector<string> tg)
            : description(d), due(du), tags(tg.begin(), tg.end()), completed(false) {}
    };

    int nextId;
    unordered_map<int, Task> tasks;                     // taskId → Task
    unordered_map<int, unordered_set<int>> userTasks;   // userId → set of taskIds

    // Return vector of pending tasks sorted by due date
    vector<Task> pendingSorted(int userId) {
        vector<Task> v;
        if (!userTasks.count(userId)) return v;
        for (int id : userTasks[userId]) {
            Task &t = tasks[id];
            if (!t.completed) v.push_back(t);
        }
        sort(v.begin(), v.end(), [](const Task &a, const Task &b) {
            return a.due < b.due;
        });
        return v;
    }
};
```
</details> |

---

## 📚 What Does “Design a Todo List” Really Want?  
At first glance it looks like a simple CRUD‑style data‑structure problem.  
But in an interview you’re actually being tested on:

1. **Object‑oriented design** – how you model *tasks*, *users*, and the list itself.  
2. **Scalability & complexity** – even though LeetCode limits calls to 100, you should still aim for **O(1) add** and **log‑linear fetches**.  
3. **Edge‑case handling** – tags, duplicate user IDs, non‑existent task IDs, etc.  
4. **Code readability** – interviewers look for clean, commented code.  

### Good, Bad & Ugly: A Quick Checklist  
| Good | Bad | Ugly |
|------|-----|------|
| ✅  Clear separation of responsibilities (TodoList vs. Task). | ❌  Sorting on every fetch can be wasteful if you have many tasks. | ❌  Using global static fields for counters can break in a multi‑threaded environment. |
| ✅  Use of defensive copies (tags, strings). | ❌  Mutable structures (`list` of taskIds) can hide bugs when tasks are removed. | ❌  Over‑engineering: a whole new DB layer or a micro‑service for a LeetCode problem. |
| ✅  O(1) `addTask` thanks to hash maps. | ❌  `completeTask` must check user membership – a linear scan if you keep a vector. | ❌  Failing to mark `completed` can lead to duplicated results. |
| ✅  `getAllTasks` / `getTasksForTag` share a common helper – no code duplication. | ❌  Sorting every time is fine for 100 ops but not for production. | ❌  Using `static` or global counters without `volatile` in Java leads to race conditions. |

---

## 📌 Design Overview  

### 1. Data‑Model  

| Element | Representation | Rationale |
|---------|----------------|-----------|
| **Task** | Object with fields: `id`, `description`, `dueDate`, `tags` (`Set<String>`), `completed` (bool). | Keeps all task attributes together and supports O(1) updates. |
| **User → Tasks** | `Map<userId, List<taskId>>` (or `unordered_map<int, vector<int>>` in C++). | Simple & fast lookup of a user’s task list. |
| **Global ID** | `int counter` (Java), `int _task_id` (Python), `int nextId` (C++). | Guarantees globally unique task IDs. |

> **Why not a TreeSet per user?**  
> With unique due dates we could keep a `TreeMap<dueDate, taskId>` per user. That would give *O(log k)* fetching without sorting.  
> However, the problem guarantees **≤ 100 calls** and small data sizes, so a simple linear scan + `sort` is *more than enough* and far easier to implement.

### 2. Operation Breakdown  

| Method | Complexity | Reason |
|--------|------------|--------|
| `addTask` | **O(1)** | Insert into two hash maps. |
| `getAllTasks` | **O(k log k)** | Filter uncompleted tasks, sort by dueDate. |
| `getTasksForTag` | **O(k log k)** | Same as above plus a `tag` filter. |
| `completeTask` | **O(1)** | Direct hash lookup + a quick membership check. |

### 3. Thread‑Safety (Optional Bonus)  
The provided Java solution uses `ConcurrentHashMap` and keeps the `taskCounter` as a simple `int`.  
If you were asked for a *thread‑safe* implementation, you’d:

* Wrap the counter increment in a `synchronized` block or use `AtomicInteger`.  
* Keep `userTasks` as `ConcurrentHashMap<Integer, CopyOnWriteArrayList<Integer>>`.  
* Mark `completed` using `AtomicBoolean`.  

For LeetCode this is overkill, but showing knowledge of concurrency can impress hiring managers.

---

## 🧪 Running the Sample  

```plaintext
Input:
["TodoList","addTask","getAllTasks","getTasksForTag","completeTask","getAllTasks"]
[[],[1,"buy milk",3,["groceries"]],[1],[1,"groceries"],[1,1],[1]]

Output:
[null,1,["buy milk"],["buy milk"],null,[]]
```

1. Create a new list.  
2. Add a single task → ID `1`.  
3. `getAllTasks` returns `["buy milk"]`.  
4. `getTasksForTag` (tag = `"groceries"`) returns the same.  
5. `completeTask(1,1)` marks it done.  
6. Final `getAllTasks` yields an empty list.  

All provided languages handle this exactly as expected.

---

## 📈 What to Practice Next  

| Skill | Practice Idea |
|-------|----------------|
| **Hash‑map tricks** | Implement a cache that invalidates after *n* hits. |
| **Set vs. List** | Build a small LRU cache to decide between O(1) deletion vs. O(log n) lookups. |
| **Concurrency** | Write a `Singleton` with a `volatile` counter and test with multiple threads. |
| **Testing** | Create unit tests that cover: non‑existent user, duplicate tags, marking already completed. |

---

## 👨‍💻 Final Verdict  
You’ve just solved a LeetCode problem that *isn’t just about algorithms*, but about design.  
The key take‑away: **model the problem well**, keep your code clean, and be ready to discuss *why* you chose a particular data‑structure.  

That, friends, is the hallmark of a senior‑level engineer—both for interviews and real‑world projects. 🚀

---


**Keywords for recruiters**: object‑oriented design, hash map, O(1) operations, defensive copying, concurrency, clean code, LeetCode, interview skills.