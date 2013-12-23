---
layout: page
title: CoreDataLinkedLists
---

You can find a writeup on how to implement a double linked list in coredata on my blog at:  
http://www.jonathansaggau.com/blog/2007/02/core_data_double_linked_list_h.html  

I will be happy to spend some time reformatting it for this wiki if there is sufficient interest.
The code is available in my svn repository at:

svn co http://jonathansaggau.com/svn/MOLinkedList

In a nutshell, I'm using the "do everything with the container" style of linked list work.  You don't message the linked list items directly for the purpose of changing order. 

hopefully this is helpful.  Take a look at the blog entry and (especially) the unit tests to see how things work.

Jonathan Saggau
jonathan _aaaat___ jonathansaggau ---daht--- com

