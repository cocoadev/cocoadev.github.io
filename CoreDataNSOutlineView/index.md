---
layout: page
title: CoreDataNSOutlineView
---

Hi,
When i want to use CoreData to repesent a tree of instances with a NSOutlineView (using a NSTreeController of course).  My gui component shows the instances as root objects and as leafs of the tree at the same time.  Does any one know how to hide instances as root objects (if they are leafs at the same time of course)?

Regards,
Philippe

----

There is an example in the examples folder that shows how to do this. You need to set a predicate on the tree controller.

