---
layout: page
title: DynamicViewHelp
---

Hi, I need to implement a view much like the Find window in the finder (the 10.3 one). Basically the user needs to be able to add additional search parameters by clicking a + button, remove them by clicking a - button, etc. The content of the search parameter controls in each row will vary depending on the selected parameters, just like in the Finder Find window. Unlike the Finder Find window, the height of each row of search parameters is not guaranteed to be constant, but will vary depending on search options, and the controls required for those options. 

My question is what is the best way to implement such a view/series of views?

--JonH

----

A handy StepWise article with source:
http://www.stepwise.com/Articles/Technical/2003-12-20.01.html

This article demonstrates using views as table cells but also has an excellent "rules editor" example as part of the views-as-tableview-cells example. -jln

----

Ok thanks, that seems a neat solution, but my understanding of NSTableView is that all rows have to have the same height. My requirements are to have rows of varying height, dependent on the content (i.e. number of controls, etc) in each view. Has anyone got any ideas?

--JonH

