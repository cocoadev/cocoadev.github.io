---
layout: page
title: CopyOnWrite
---

Copy On Write (also known as COW) is a technique where an address space is copied during a fork() operation, where the child process has a copy of the parent's address space.  What happens under the hood is that the parent and child share the same pages in memory (so no actual data is duplicated.  just pointers to pages in memory).  If either process writes to a page, the writing process gets a copy of the page. That way the only pages of process memory that actually get copied are the ones that are modified.

Since most times the child is just going to exec*() anyway and blow away its current address space, this is a real timesaver.

