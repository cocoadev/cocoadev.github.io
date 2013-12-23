---
layout: page
title: DirectedCycle
---

A directed graph is a collection of vertices (points) and a collection of directed edges (lines with arrowheads) joining them. A directed graph, like many abstract mathematical concepts, is useful because of the properties it makes obvious.

A DirectedCycle is simply a sequence of directed edges each of which ends where the next one starts, and the last one ends where the first one starts, so that you can follow them around forever.

DirectedCycle**'s are a useful concept in programming because many dangerous situations occur if and when they arise. Let us consider two examples.

----**Retain-release**----

Consider all the objects your application creates as a set of points. If object A retains object B at some point, and releases it only when A is deleted, we connect them by a directed edge from A to B.

Now consider what happens if a DirectedCycle arises in this graph - say the cycle is A->B->C->D->A. At some point, A retains B; at some point, B retains C, and so on. Now, in order for A to release B, it must first be deleted, which occurs *after* D releases it. To do this, D must be deleted, which only occurs after C has released it. This must be preceeded by the deletion of C, which cannot occur until B has released it. But guess what? This cannot happen until B has been deleted, which must be preceeded by A releasing B.

This cycle means none of A, B, C, or D will ever be deleted; memory is "lost" forever. DirectedCycle**'s on such graphs are also called RetainCycles.

----**Locking**----

Now suppose our multithreaded code has a series of locks, and sometimes a piece of code grabs more than one lock at once. Represent each lock by a point in our directed graph, and connect lock A to lock B by a directed edge if some piece of code tries to grab B whilst holding A at any point.

DirectedCycle**'s in such a graph usually herald DeadLock potential in the program. The longer such a cycle is, the less likely to occur, but the best way to preclude DeadLock is to make sure no DirectedCycle occurs.

