---
layout: page
title: OutlineViewsWithMultipleTriangles
---

Is it possible to have an  NSOutlineView with two triangles in one row, where each triangle expands a different data set?

----

Not without an undue amount of hackery, and it sounds like a UI nightmare to boot.  I'd suggest setting the row to have 2 children (one for each data set) and then make the data sets children of those child rows.  -- Bo

*Why would you want to do it at all? Chances are, it'll confuse your users.*

