---
layout: page
title: AddingRows
---

The developer documentation says the addRow method adds a row to a matrix below the last one.  In my app everytime I call addRow it adds a cell above the last one.  This is more of an asthetic thing but does anyone know why this is happening?
----
OK I looked a little closer, it does add below, but it pushes the first row up.  What I need to do is keep the first row where it is and have added rows add below, do i need to just move the entire matrix down every time i add a new row??
----
You're probably running into the Cocoa Coordinate System. Since the origin (0, 0) is normally at the *bottom* in Cocoa, if you increase something's size without moving its location, it will grow *upwards*, which is the opposite of what people coming from other systems expect. So the behavior you're seeing makes sense, and moving it back down afterwards is perfectly reasonable.

----

Except that NSMatrix has a reversed coord system.  ie: 0,0 is at the top left of an NSMatrix.  is the matrix a part of some more complicated control or are you working with the NSMatrix class directly? (browser, tableView, etc...)  the reason I am asking is that the behavior you are describing does not make sense to me and I suspect that your matrix is in a scrollView, or that perhaps there are other extenuating circumstances.

*When a view is flipped, that only affects things inside it. The fact that NSMatrix uses a flipped coordinate system has no influence on what happens when you change its size, because the NSMatrix's frame exists in its superview's coordinate system. I maintain that the behavior being seen is normal and reasonable.*

----

No special view just the main window. I have a button that adds a new cell (NSMatrix of combo boxes) when pressed.  I have dug around a bit and it seems that combo boxes and matricies do not currently behave very well.  It seems that combo boxes no longer behave as combo boxes when part of a matrix.  I doubt this has anything to do with my current problem but it is still bothersome.

----

It may be reasonable for the matrix to "appear" to move up, but is there a simple solution to that new rows "appear" to be added at the bottom of the matrix?

----

You can manually reposition the matrix after you add the new cell. Assuming the matrix's superview is NOT flipped and you want the top-left coordinate of the matrix to stay the same, that means you want the maximum Y coordinate of the matrix's frame to stay the same. Use this code instead of just calling addRow.

    
// Save the current frame
NSRect originalFrame = [matrix frame];

// Add the new row
[matrix addRow];
[matrix sizeToCells];

// Adjust the frame's origin so that the maximum Y coordinate
// matches originalFrame, but using the new frame's size
// origin + height = maximum Y coordinate, therefore
// origin = maximum Y coordinate - height
NSRect newFrame = [matrix frame];
newFrame.origin.y = NSMaxY(originalFrame) - NSHeight(newFrame);
[matrix setFrame:newFrame];

// The matrix and its superview need to be redrawn
[matrix setNeedsDisplay:YES];
matrix superview] setNeedsDisplay:YES];



- [[ChrisCampbell

----

You can use simple math to ajust your matrix's coordinates using its cell's dimensions. Assuming your matrix's origin is in its lower-left corner, you simply subtract the cell's height and the intercell spacing from your matrix's Y origin:

<pre>
NSRect rect = [theMatrix frame];
rect.origin.y -= [theMatrix cellSize].height;
rect.origin.y -= [theMatrix intercellSpacing].height;
[theMatrix setFrame:rect];
</pre>

- Bwass

