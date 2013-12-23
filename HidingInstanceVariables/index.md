---
layout: page
title: HidingInstanceVariables
---

How would one go about hiding the InstanceVariables in the header for a class like Apple does with many core Cocoa classes?  

For example, if you look at NSArray.h, there are no InstanceVariable declarations visible, only methods. How would I do the same in my classes?

-- BrianMoore

NSArray is a class cluster; the arrays you actually use are a subclass of NSArray. Hence why NSArray itself has no instance variables. Check out the ClassClusters tutorial for how to roll your own. -- KritTer

Ah... I see. Thanks!

-- BrianMoore

