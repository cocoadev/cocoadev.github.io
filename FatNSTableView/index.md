---
layout: page
title: FatNSTableView
---

I would like to make an NSTableView with "fat" cells. Something a bit like the feeds view in NewsFire. I don't need the NSOutlineView behavior they use there, but I'd like my cells to be taller than usual, so as to allow a line of text below the main text. I know I'll probably have to subclass the NSCell that goes into the NSTableView but I'm not quite sure how to actually do that.

Thank you!

----
Whoops! I just saw it's as simple as changing the rowheight in IB :) And to add the line of text underneath, it should be as simple as using an NSAttributedString as the cell value, right?

Whee!
----

Make sure the table cell is an NSTextFieldCell. I couldn't get multi-line cells to work without it. Oh, and just use a newline tag (type "\n" - without the quotes) to do a line break...

Also might want to look into just custom drawing the cell instead of the attributed string -- it's easier in some cases with per pixel tweakage, etc.

