---
layout: page
title: NSControllerTricks
---

How do I do X with an NSController?
----
How do I clear the data from an NSArrayController before I fill it with new data?

the NSArrayController inherits from NSObjectController, which implements setContent.  If you call

[ myArrayController setContent:nil ]

before filling in the new array, then the previous contents will be removed

--AlexBrown
----
How do I designate an action to be performed when I double-click on an NSTableView attached to an NSController?

A: Correct me if I am missing something, but I beleive you still do it the normal way, target/doubleAction. You have to hook it up manualy in code.

----
How do I use an NSOutlineView with an NSArrayController?

*It seems that you can't.  I was just looking for the same answer.  See OutlineViewBindings.  --Mark*

