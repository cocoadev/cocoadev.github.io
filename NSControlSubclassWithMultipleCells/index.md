---
layout: page
title: NSControlSubclassWithMultipleCells
---



Alright, bear with me here.  In my app, I have an array of model objects that I need to display in a very particular way.  Basically, each model object can be represented as a short string of text.  These objects also have an order, and they need to be laid out next to their number.  Finally, it is required that this list, when displayed, is shown in columns and wraps around certain rectangles.

What I've done so far is create an NSView subclass that uses the NSTextStorage/NSLayoutManager/NSTextContainer/NSTextView system.  Whenever the model objects are changed, it creates a fresh NSTextStorage object with their data.  Then, I use NSTextContainers and NSTextViews to put the list into columns and wrap it.  It seems like the appropriate thing to do, the only downside is that I have to recreate NSTextStorage every time the model objects change.  I thought about having each object using NSRange to keep track of where it was in the NSTextStorage object, and then it would only have to replace the characters in that range.  But that doesn't work, since changing one part of the NSTextStorage will invalidate all the NSRanges after it.

But that's not my question.  What I'm doing now is not actually what I want.  What I'd like to do is allow the user to click on the list item directly, and have it bring up a NSTextCell so that they can edit the value directly.  Basically like how you change file names in Finder, e.g.  So I thought I'd look into making the whole thing a subclass of NSControl, and have each list item be a subclass of NSTextCell.  I ran into problems when I tried to read the documentation on how to do that.  I'm completely stumped on how to make an NSControl with multiple cells.  I can make one cell, and use the setCell: method to place it into my control, and I've even overridden drawRect: to draw the cells in the appropriate place.  But I'm hitting a snag with mouse events, since the default implementation of NSControl only sends mouse events to the cell set in setCell:, and even then it sends the event for every mouse click inside the control.

What's more, if I do figure out how to have multiple cells in a control, they still need to be laid out as if they were columns of text.  That means that if I have a list, say,

* 1) Beans
* 2) Milk
* 3) Rice pudding


And if "pudding" should get wrapped over to the next column, then it should still be part of the same cell as "3) Rice".  I'm beginning to think I have a lot of programming ahead of me... --sk
----
Check out NSTableView :). Seriously, this does just about everything you want, except that if the text wraps it won't just show up. If that doesn't work, see if you can use NSMatrix before making a custom view. Also, just use NSTextFieldCell for the layout, instead of NSTextStorage/NSLayoutManager/NSTextContainer/NSTextView. These are REALLY complicated classes, and NSTextField is the "simple" version of all of these. The little editor you want is actually an instance of NSTextView called the *field editor*, which is automatically resized over your text field when you want to edit it. Take a look at http://developer.apple.com/documentation/Cocoa/Conceptual/TextArchitecture/index.html for more information. --JediKnil
----
Well, I'm actually using an NSTableView now, but that's not my optimal solution.  As it stands, the user can pop open a sheet with a table view on it, edit the data directly, click OK, and the data gets laid out again.  But I'd rather have the user be able to edit it in its formatted form.  The key is that the formatting of this list won't fit into a table view.  Thanks for the info on the field editor... I think maybe I can just use that and tracking rectangles to get something like what I want.  --sk


----

sk, 
get into Tableviews.  you are wasting your efforts re-inventing them.  Seriously.  the only thing that appears to be missing from the default tableView is the automatic resizing of the line if the word wraps.  Thats it.  and its going to be ALOT easier to add that functionality than to rebuild everything else.

lets clear something else up too.  An NSControl that has multiple cells in it, is CALLED an NSMatrix.  Thats what it is, and it already works much better than your homegrown version (trust me here, Apple/NEXT has had 12 + years on its development, and countless man-hours on its upkeep). NSTables are derived from NSMatrix, so theres a little more UI for the user, but if you really don't want some of the issues dealing with an NSTable, Then you have no reason to ignore NSMatrix.

in addition, While I don't pretend to completely understand every tiny detail of what you are working on, I have made comparable tables in Core Data based projects that  require NO PROGRAMMING At All. 

Doing what you are doing for a learning challenge is one thing, but if you have goals beyond the example you have given, then I think you have lost your way my friend.
You NEED NSTableView, Bindings, and Core Data. 

----
"*An NSControl that has multiple cells in it, is CALLED an NSMatrix.*"

This is outright inaccurate and just plain unhelpful. Sure, an NSMatrix has multiple cells, but so does an NSTableView, an NSOutlineView, etc. An excellent example of a control that uses more than one cell (but is not a matrix or a list) is the standard search box. Its got a text cell and two button cells (the magnifying glass and the cancel button). Also, your suggestion does not satisfy one of his requirements (multiple-column/wrapping).

"*NSTables are derived from NSMatrix ...*"

Also inaccurate (as in "completely wrong"). NSMatrix and NSTableView are "derived from" (subclasses of) NSControl. 

"*You NEED NSTableView, Bindings, and Core Data.*"

Completely false. Cocoa Bindings is an excellent and impressive piece of architecture but thousands of Cocoa applications have been written without it. There's nothing wrong with data sources and glue-code (the "old-fashioned way") and the bindings architecture has **NOTHING TO DO** with this problem. Core Data is also independent of the bindings architecture and likewise has **NOTHING TO DO** with this problem (and nothing "directly" to do with bindings for that matter, they just work very well together via KVO/KVC).

Your response was unhelpful and misguiding. Please research your proclamations before posting them, especially if you're going to take on a lecturer's tone.

