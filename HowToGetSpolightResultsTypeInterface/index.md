---
layout: page
title: HowToGetSpolightResultsTypeInterface
---



I need to create an interface which has the same style as the Spotlight results window.  Collapsable segments which contain UI elements and a title at the top.  My requirements are a bit different in that it wouldn't just be a file list, but UI elements like sliders and buttons which are contained in each segment.

Any ideas?

----

NSOutlineView might be a good place to start.

----

ok, NSOutlineView is a bit of a pain to use, but I have a simple list working.  Now I'm ready to put in my UI controls and I'm a bit unclear on how to go about it.

In my NSOutlineViewDataSource class, I'm returning a set of 4 strings as the children of the root node, thereby creating 4 expandable sections.  Now I need to have 4 NSViews be shown when those sections are expanded.  I've created these views in Interface Builder and connected them to outlets in my NSOutlineViewDataSource class.  Now I'm trying to just return them in the call to [NSOutlineViewDataSource outlineView:objectValueForTableColumn:byItem:], hoping that the NSOutlineView will know how to deal with NSViews, but unfortunately, no such luck.  I'm thinking I need to delve deeper into the bowels of NSOutlineView and it's superclass NSTableView and possibly create a subclass of NSOutlineView which overrides a few methods so that it can render an NSView as a table element.

A good example of what I'm trying to do is to right click on a file in the Finder and select "Get Info".  In the dialog window which the Finder pops up, you can expand and collapse sections of the UI.

----
It is impossible (or rather, incredibly difficult) to do this with NSOutlineView, since it uses NSCell, not NSView for content. It's been discussed how to add NSView to NSTableView and NSOutlineView, but generating an entire interface for it is near insane, IMHO. You'd be better off going with a simple NSView containing a subordinate NSView for each section. Then, put NSButtons that are set to Disclosure Buttons in IB above each section and a title if you wish, and then in your controller connect everything and have the buttons show or hide their respective views, adjusting the X and Y positions of the others as it goes. I believe this is how Finder (even though it's Carbon, same concept however) and all the OmniApp inspectors do it. --LoganCollins
----
Yes, that sounds much easier.  I have seen people talk about putting an NSView in an NSCell subclass, and render it from there, but then I'm unsure of how you'd make NSOutlineView use one cell size for the section titles, and another cell size for the for the subviews.

----
- (float)outlineView:(NSOutlineView *)outlineView heightOfRowByItem:(id)item;
----
Thanks for all the help.  It looks like I have two options ahead of me here then.  First is to use NSOutlineView, NSOutlineViewDataSource and a subclass of NSCell which handles rendering of an NSView.  Second is to use Disclosure Buttons and expand/collapse segments programmatically.  First way buys me automatic expanding and collapsing, but requires custom rendering for the NSView.  Second way gives rendering for free, but requires me to handle positioning/sizing of views.

