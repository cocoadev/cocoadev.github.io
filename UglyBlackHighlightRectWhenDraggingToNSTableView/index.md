---
layout: page
title: UglyBlackHighlightRectWhenDraggingToNSTableView
---

I looked all over the General/NSCell class but can't figure out what to change to modify that ugly black highlight rectangle that you get when you drag something onto it (eg onto a tableview row).  What I want is something like the Finder in Panther, a nice cell highlight.  What a strange choice for default behaviour in this class with no apparent way to overrule it!

General/EcumeDesJours

----
It looks like you might be able to do it by overriding General/NSTableView's -drawRow:clipRect:

no luck with that one - General/EcumeDesJours

----
I was reminded of this ongoing and unresolved issue by the revival of the General/RoundedRectRowHighlighting thread...   

----

Has anyone found a solution?  Clearly Apple is doing it somehow in Mail.app and Address Book -- the black highlight is killing me!

----
    - (void) _drawDropHighlightBetweenUpperRow: (int) parameter1 andLowerRow: (int) parameter2 atOffset: (float) parameter3

...maybe? --General/RyanStevens

----

I seem to have it working using the following.  Feel free to make changes to anything (especially the colors, I just did it by eye). -- General/AustinSarner

    static float widthOffset = 5.0;
static float heightOffset = 3.0;

-(void)_drawDropHighlightOnRow:(int)rowIndex
{
	[self lockFocus];
	
	General/NSRect drawRect = [self rectOfRow:rowIndex];
	
	drawRect.size.width -= widthOffset;
	drawRect.origin.x += widthOffset/2.0;

	drawRect.size.height -= heightOffset;
	drawRect.origin.y += heightOffset/2.0;
	
	General/[[[NSColor blueColor]colorWithAlphaComponent:0.2]set];
	General/[NSBezierPath fillRoundRectInRect:drawRect radius:4.0];

	General/[[[NSColor blueColor]colorWithAlphaComponent:0.8]set];
	General/[NSBezierPath setDefaultLineWidth:2.0];
	General/[NSBezierPath strokeRoundRectInRect:drawRect radius:4.0];

	[self unlockFocus];
}

adding that to my General/NSCell subclass gives me 'invalid initializer' errors when it goes to compile.  Anyone else get this to work?

It should be in your General/NSTableView subclass. :)

----

**Just to be clear:  the above is overriding a private method, so may well break with future versions.**

Also: the above uses +fillRoundRectInRect which is a General/NSBezierPath Category Method found on the General/NSBezierPathCategory page.

It worked for me, and has been a longstanding issue so I am very grateful to Ryan and the others for this solution - General/EcumeDesJours

*I'm sorry to emphasize this again, but "worked for me" is really not the issue here.  This is using an internal detail of the General/NSTableView implementation, so cannot be relied on.  If you can deal with the consequences, then fine, but it's important to realize what's going on.  Every time you use a private method, you limit Apple's ability to improve the frameworks.*

of course, it would be best if we didnt have to hack apple's stuff but no 'legit' way to override this has surfaced...:-/

----
Using undocumented API (/internal details) does not limit Apple's ability to improve the frameworks. If I was Adobe or Microsoft (neither of which ship Cocoa apps but whatever), it might affect their framework design decisions but even then I think they'd continue doing what they're doing. The original disclaimer was good enough -- It could break in future versions. If it breaks you'll know and if you're smart your app will continue to work regardless. Good thing it's not a "critical" feature.

----

How do I make the drag function not highlight the table or any cells?

----

*Why? they highlight for a reason (to indicate they're valid drop targets). Try turning the focus ring off.*

I highlight the cell itself instead.

This is unfortunately a longstanding unsolved problem, as discussed above

General/EcumeDesJours

----

I have a possible solution:

Subclass General/NSTableView and leave setDropRow:dropOperation: blank

and a second solution:

override this one:

(private)

- (void) _drawDropHighlightBetweenUpperRow: (int) parameter1 andLowerRow: (int) parameter2 atOffset: (float) parameter3
{}

*did this work for anyone?*

**Obligatory warning about overriding private methods...**

----

sure enough, _highlightColorForCell is gone in Leopard...  back to the drawing board!

----

shockingly, there is a new private method that get rid of those black rectangles in Leopard, which is never a good idea as things may break in the future. However, for educational purposes, you can try overriding the following in your General/NSTableView subclass: _drawDropHighlightBackgroundForRow: as in

- (id)_drawDropHighlightBackgroundForRow:(id)unObject{ return nil; }
