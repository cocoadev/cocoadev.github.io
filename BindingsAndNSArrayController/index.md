---
layout: page
title: BindingsAndNSArrayController
---



I have a NSTableview with 4 columns (all ints) and I have a NSArrayController (bound to my class called Levels) and using bindings I've got a add and remove button to insert and remove data from that table, which works fine.  

How is that data accessed from code?  For example, if I want to just print out all the objects that I've added to that table.  What name does it instantiate a new instance of my class Levels?  

----

Solved...  long story short, revamped class to a mutabledict and now have full access with keys.  Sorry for wasting this space; this can be deleted because I feel it won't help anyone.

