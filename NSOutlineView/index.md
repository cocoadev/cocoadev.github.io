---
layout: page
title: NSOutlineView
---

NSOutlineView topics:
[Topic]

NSOutlineView inherits from : NSTableView  :  NSControl  :  NSView  :  NSResponder  : NSObject.

http://developer.apple.com/documentation/Cocoa/Conceptual/OutlineView/Articles/Art/outlineview.gif

Class Reference for NSOutlineView at ADC:

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSOutlineView_Class/Reference/Reference.html

Conceptual Docs:

http://developer.apple.com/documentation/Cocoa/Conceptual/OutlineView/index.html

Docs for the <NSOutlineViewDataSource> Protocol:
http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Protocols/NSOutlineViewDataSource_Protocol/Reference/Reference.html
http://developer.apple.com/documentation/Cocoa/Conceptual/OutlineView/Articles/UsingOutlineDataSource.html
----

There are samples for NSOutlineView in your local developer's documentation:

file:///Developer/Examples/AppKit/OutlineView/

and

file:///Developer/Examples/AppKit/DragNDropOutlineView/

For a more human explanation of how to implement an NSOutlineView see this post on the Apple Cocoa-Dev list:

http://cocoa.mamasam.com/COCOADEV/2002/03/1/27771.php (Dead Link)

----

The description for **setIndentationMarkerFollowsCell:** is confusing. It should read:

*Sets whether the indentation marker symbol displayed in the outline column should be indented along with the  cell contents, or always displayed left-justified in the column.  The default is YES, the indentation marker is indented along with the cell contents.*

----

NSOutlineView asks the DataSource for the number of items (and the actual items) by sending NIL as the item. Thus, in your datasource, you should create an array of the 'root items', and if item is nil, return one of those. This allows you to have a 'rootless' NSOutlineView, which is far more useful than the root-ed one Apple sends you with the OutlineView example.

*How would you do this?*

- (int)outlineView:(NSOutlineView *)outlineView numberOfChildrenOfItem:(id)item

if item is nil, return the number of root items,


- (id)outlineView:(NSOutlineView *)outlineView child:(int)index ofItem:(id)item

if item is nil, return the nth root item

----

How do you make a gradiented outline view? ScotlandSoftware has some sample code to make a table view use a gradient, but it doesn't work well with outline views: the leftmost part, the one with the disclosure triangle, is not painted. Any ideas?

*See http://amonre.org/2005/07/30/using-a-nsoutlineview-as-a-source-list/ *

----

I found that Wil Shipley's discussion of the NSTableView subclass with special highlighting at http://wilshipley.com/blog/2005/07/pimp-my-code-part-3-gradient.html seems to be the most useful. For a general NSOutlineView example, I couldn't understand the pages that the above links led to, but Aaron Hillegass' example on http://www.bignerdranch.com/examples.shtml is really good, really simple, and easy to understand. If you aren't getting the examples that Apple provides, you might like the Big Nerd Ranch example, as I did.

----

I've got a question about RecursiveSelectionOfCheckboxesInNSOutlineView .  I was hoping someone could help me out.

----

I'm wondering if someone could help me with my NSOutlineViewMemoryLeaks

----

If you're changing the height of rows in an NSOutlineView using setRowHeight and you find your cell drawing is getting screwed up, try calling reloadData after calling setRowHeight. That seems to fix the problem (found and tested under 10.4.8). This doesn't seem to be documented.

----

NSOutlineView implements some drawing optimizations during live resizing (unlike NSTalbeView). For some NSCell subclasses this can cause drawing artifacts. You can disable the live resize optimizations by subclassing NSOutlineView and returning NO from - (BOOL)inLiveResize.

----

How can I get the output of the nodes in the XML format

----

Here's the new URL to Aaron's example of how to use NSOutlineView, called OutlineMe: http://www.bignerdranch.com/source/outline.tgz
 (Dead Link)
----

The following worked better for me than overriding inLiveResize:

    
@implementation MySourceListOutlineView

- (void)resizeWithOldSuperviewSize:(NSSize)oldSize
{
	[super resizeWithOldSuperviewSize:oldSize];
	// circumvent drawing optimizations that prevent NSOutlineView from live resizing
	if ([self inLiveResize])
		[self sizeLastColumnToFit];
}

@end


----

I've written an example NSOutlineView datasource that is very simple and should be easily followed: http://www.stupendous.net/archives/2009/01/11/nsoutlineview-example/

The basic idea is that the root node is an NSDictionary, with a tree of NSDictionary/NSArray/NSString objects from that. Modify the root node as you see fit (or load it's data from a plist or whatever) then send a reloadItem message to the NSOutlineView.

