---
layout: page
title: DrawerFolders
---



Does anyone know how to create folders in a drawer?  Like the ones in the Mail app?  I've looked but havn't found anything on the subject (I'm also not really sure how to describe what I'm looking for very well).  If someone could point me in the right direction that would be great.

Paul


You're asking the wrong question.  What you need to do is get images (and text, presumably) into an NSTableView (or NSOutlineView if you want to do hierarchy).  Basically, all you have to do is return the correct cell type in the data source object... check up on that and you'll have all the info you need.

