---
layout: page
title: MaximumAmountOfItemsInNSTableView
---

My application is powered by SQLLite in the backend so handling millions of objects should not be a problem, however apparently NSTableViews do have such a limit.

Does anyone here know the exact limit of items in an NSTableView so that I may if at all possible notify the user when some of the items will have to be removed (Thank god for the search function)?

----

Well, since NSTableView's delegate method     numberOfRowsInTableView: returns an int, I'd imagine it would be the maximum amount of an int or unsigned int.

Also, since you might *need* a large database, you could notify the user that some items "cannot be displayed" and have buttons to scroll through the next four billion items (touch of sarcasm there :). The highest value for     unsigned int is 2^32 - 1 (4,294,967,295), and the highest value for a normal (    signed)     int is 2^31 - 1 (2,147,483,647), with one "sign bit" reserved. On a side note, the *lowest* value for a     signed int is negative 2^31 (no "minus one"), to avoid having a different integer value for "negative zero." Also note that these values are only true with 32-bit integer types -- if you use a platform with 64-bit     ints, like the G5 (I think), the maximum values are *much* larger. --JediKnil

----

I would be really surprised if NSTableView could handle anywhere near INT_MAX items.

One limit will be the Cocoa coordinate system. An NSTableView is implemented as a subview of an NSScrollView, and so its max Y coordinate will increase with the number of items in the table. The Cocoa coordinate system is built on floats, and things will probably stop working nicely once the magnitude gets large enough that a float cannot represent individual pixels. A float's fraction section is 23 bits long, so coordinates above 8388608 will break. With a row height of 12, you get 699050 items before you go over.

----

Additionally, an NSTableView becomes completely unusable long before any hard limit is reached.  Think about scrolling with a large number of elements:  The scroll bar only allows gross movements.  Page-up and page-down only move a screen's worth at a time.  Without anything in the middle, scrolling is pretty much impossible.

----

How many items should then be allowed per "section" to maximize user experience? 1000? 10000?

----

Perhaps you'd be better off implementing a search engine if you're really displaying millions of items? Expecting a user to find an item amidst a million items is a bit much.

----

We already have that, but we wish to have browsing also, i'm planning to have A-H H-P P-Z (for example) but I wish to dynamically generate these lists based on a usable length. What should a realistic maximum be?

**You could let the user choose, but then you still need a default.**

----

I think a realistic maximum should be one that still allows the scroll bar to be useful. This would mean that moving the scroll bar by one pixel would move your table's view by a certain reasonable amount, perhaps a third of a page at most. You can then calculate a maximum number of records from your table's vertical size and your row height. I wouldn't recommend making this dynamic if your view is resizeable, just pick a good maximum for a normal height and stick with it.

----

Using that logic I have concluded that having a limit of 1000 items by default per category gives the best performance and usability, thanks all.

----

Mm, I disagree with that logic.  At one pixel = a few pages, a table view is still plenty usable.  The problem is when page-up and page-down are no longer on the same scale as the scroll bar.  My main archive mailbox in Mail has 15000 messages, and it is not a problem for me.

