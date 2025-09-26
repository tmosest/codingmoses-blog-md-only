---
title: LeetCode 3243. Shortest Distance After Road Addition Queries I - 
description: Placeholder
date: 2025-09-21
categories: []
author: moses
tags: []
hideToc: true
---
        **Problem Overview**

You have a directed graph with \(n\) nodes, numbered \(0\) to \(n-1\).  
Initially the graph contains a simple chain of edges  
\(0 \rightarrow 1 \rightarrow 2 \rightarrow \dots \rightarrow n-1\).

You receive a sequence of queries.  
Each query is a pair \((u, v)\) meaning “add a new directed edge \(u \rightarrow v\) to the graph”.

After each new edge is added, you must compute the length of the shortest path from node 0 to node \(n-1\).  
Return a list of these shortest distances (or \(-1\) if no such path exists) for every query.

In short: **incrementally update a directed graph by adding edges and recompute the shortest 0‑to‑\(n-1\) path after each update.**