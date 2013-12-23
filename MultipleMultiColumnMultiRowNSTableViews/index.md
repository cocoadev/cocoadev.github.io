---
layout: page
title: MultipleMultiColumnMultiRowNSTableViews
---

Hey

I need to create multiple table views or multiple matrix's inside of a table view or matrix's. E.g. I would like to have one encompassing, lets say NSMatrix. Each cell in the Matrix would contain a NSTableView or another NSMatrix.

Does anyone have any suggestions on how to go about doing this? As it stands I am at the point where I am thinking I will have to write my own version of a NSMatrix that instead of using cells, uses NSViews.... This might turn into a time consuming process :(

Thanks 

----

This seems like a situation where you should probably seriously rethink your GUI approach. In any case, check out this example:

http://www.stepwise.com/Articles/Technical/2003-12-20.01.html

This allows views to take the place of cells in a table view. The concept is exactly the same -- you want views to replace the cells in NSMatrix.


----

Thanks thats exactly what I am looking for :P. As for the GUI approach... its an experiment and I will see where it goes. It may not turn out the way I would like.

