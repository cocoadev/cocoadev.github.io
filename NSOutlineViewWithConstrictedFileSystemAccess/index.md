---
layout: page
title: NSOutlineViewWithConstrictedFileSystemAccess
---

I'm trying to load the contents of only a certain folder (such as /Developer/Examples and it's siblings) into an outline view. I got this working so far using OutlineView example at file:///Developer/Examples/Appkit/OutlineView. The problem is, that I need to be able to change the volume that the data source gets the data from. I want to be able to change it on the fly from say: /Developer/Examples to /Volumes/FireWire HD/Developer/Examples. Can anyone give me any hints/tips on how to do so?

Thanks in advance

Bob.

----

Just remove all objects in your datasource, and call a method to load all items in another folder. When I do NSOutlineView's, I have a node object which can contain children (in a mutable array), and can perform methods on the children. So you have a main node, which contains other nodes (each node being a folder or file). Then to reset it all, just erase all nodes in your mutable array, and recreate them again using a different folder.

