---
layout: page
title: NSSearchFieldTemplateFaulty
---



Hiyo everyone. The strangest bug has just presented itself to me today when I upgraded to XCode 2.2. I'm pretty sure it wasn't happening before then, but it is now. I have an NSSearchField to search through my document, and it works just dandy, with one small exception. I have the search template set up with the "Recent Searches" "Recent Item" "Empty Searches" "No Recent Searches" template - using tags 1000, 1001, 1002, and 1003 respectively. It works for the most part - if you search, your search gets put into the list, except for some reason the "Recent Item" template menu item isn't changing it's name. I have it titled "Item" and tagged 1001, and up until a little while ago, the title of the recent searches was the same thing as the search query. Now, if I select "Item" in the list, it will perform the same query as it did before, so the representedObject is still the same (I assume that's what they use). For some reason though, the title just won't update.

In my subclassed NSSearchField, I tried to trap validation of these Recent Items in order to change their title, but I'm afraid I can't validate them - it only checks the other parts of my menu. So, yeah, in summary (in case my explanation didn't make sense), my template looks like this:
    
Recent Searches (1000)
Recent Item (1001)
-------------------------- 
Clear Recent Searches (1002)
No Recent Searches (1003)


When I run my application and search for "bob", I get this:
    
Recent Searches
Recent Item
--------------------------
Clear Recent Searches

And when I select "Recent Item", it then searches for "bob".

Now, I know that if worst comes to worst, I could re-write the behavior myself... but I'd really prefer not to. Has anyone had any experience with this sort of bug, and know how to work with it? I searched here, the cocoa mailing lists... never seen any bug like this before. Of note, I'm pretty sure a backup of mine from 2 days ago didn't have the bug until I recompiled it. Oh, and if I made the field a regular NSSearchField, the bug still comes up, so I don't think it has to do with the 20 or so lines in my subclass.

Anyone have any ideas?
----
You shouldn't call setAttributedTitle: for menu items with tag 1001 in NSSearchField menu templates. Search field adds recent items by calling item's setTitle: method. But NSMenuItem's title attribute isn't used at all if attributedTitle attribute is set.

