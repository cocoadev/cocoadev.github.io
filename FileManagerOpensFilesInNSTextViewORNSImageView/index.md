---
layout: page
title: FileManagerOpensFilesInNSTextViewORNSImageView
---

How would I create an NSOutlineView that handles files like XCode?

----

Interesting how the subject and contents are totally different.

Here is the answer though:

In the developer folder, /Examples/AppKit/OutlineView. Just add some custom NSCell's into the outlineview and you should be able to produce everything you need. -- MatPeterson
----
I want to know how to I double click a row, then open in the proper NSView depending on its file type.

----

You mean to display the contents of the file? If you are using an NSSplitView, you just need to switch the views. Perhaps something like this:

    
[splitView replaceSubview:oldView with:newView];


-- RyanBates

