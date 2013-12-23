---
layout: page
title: CustomTableCellEditing
---

Hello there, I've been working on an application that uses one outline view and one table view, I use a custom cell to display the information for each row, however I also need the editor to show in a specific rect.

Does anyone here have any experience in this area, I've been reading the docs and there doesn't seem to be anything specific on the subject.

----

*Search your local copy of the Apple example code (in /Developer/Examples) for "ImageAndTextCell" ... I believe this has an example of what you're looking for.*

----

Indeed, the key methods on NSCell are:

- (void)editWithFrame:(NSRect)aRect inView:(NSView *)controlView editor:(NSText *)textObj delegate:(id)anObject event:(NSEvent *)theEvent;

and 

- (void)selectWithFrame:(NSRect)aRect inView:(NSView *)controlView editor:(NSText *)textObj delegate:(id)anObject start:(int)selStart length:(int)selLength;

The rect that you're passed is the cell rect... If your editable area is some subsection of the whole cell area, then you can override these methods to restrict the text editing to a smaller "aRect" based on the whole cell's area...

-- MrO

