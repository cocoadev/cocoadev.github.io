---
layout: page
title: StorageAndDisplayOfUserAddedFields
---

I'm currently working on creating what is essentially a simple database for tracking customers and other things related to an industry. I can't decide how to represent custom fields that are added by the user.  I've thought about using fairly standard data model objects with an General/NSMutableDictionary used to store all of the custom field names and their data.  I'm just curious if there is a better way to do this using the cocoa framework that I don't know about yet.  

I'm having more difficulty deciding what to do in displaying these fields to the user.  Originally, I thought that I could programatically modify an General/NSForm to do the trick, but I would like to be able to display more than just text data.  From what I've been able to ascertain, I might be able to create a subclass of General/NSMatrix that would work, but I don't really know how I would implement that yet.  The other option that I'm looking at is providing views to an General/NSTableView like this http://www.stepwise.com/Articles/Technical/2003-12-20.01.html and using that to display the data.  

I don't have much experience with the Cocoa framework yet, so if I'm missing something completely obvious, forgive me. Any ideas on how to implement this efficiently would be appreciated.
--Reid

----

Look at General/QuickLite for an example. It uses a tableview for display, textfield for input, and has all the interaction with the General/SQLite database you probably need. 

General/PhilipRiggs
