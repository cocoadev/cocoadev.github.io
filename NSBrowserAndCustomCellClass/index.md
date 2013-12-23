---
layout: page
title: NSBrowserAndCustomCellClass
---

Hi All, I've been attempting to use a custom cell within the NSBrowser but so far I have been unsuccessful. I subclassed NSCell, and called this on the NSBrowser:
    
[mainBrowser setCellClass:[jazzTrackerCell class]];

I've attempted the setCellPrototype: method and allocating jazzTrackerCell with no luck either. The browser doesn't use the custom cell at all!
How do I go about using a custom cell in the NSBrowser? Thanks!

----

I have some code which uses custom cells in an NSBrowser. Here is my code in awakeFromNib:

    
  CustomCell* thePrototype = [[[CustomCell alloc] init] autorelease];
  self theBrowser] setDelegate:[self theDataSource;
  self theBrowser] setCellPrototype:thePrototype];
  [[self theBrowser] setMatrixClass:[[[BrowserMatrixView class]];


The "CustomCell" class has NSBrowserCell as a superclass. Not sure you need to have a custom matrix class - it's for drag and drop and right clicking on the cells (see RightClickSelectInBrowser). Let me know if you need any more code - DavidThorpe.

----
This is probably the same problem as CustomSegmentedControls.

----
To paraphrase from the CustomSegmentedControls page, unless you've created and loaded an IB palette for your custom cell and control, IB doesn't know about them when it archives your nib.  It archives whatever +cellClass returns.  Even if you provide a custom class in the Inspector, without a palette IB can only call +cellClass on the version you dragged into the nib, since the code for your custom control isn't loaded.  [[NSSegmentedControl] cellClass] returns NSSegmentedCell, so that's the class that IB archives in the nib.

The way you go about fixing it involves overriding your control's -initWithCoder: method to temporarily modify how the NSKeyedUnarchiver unpacks your control -- this will only work for 10.2+ nibs.  When NSNib unloads your control, it calls this method.  So you tell the NSKeyedArchiver which you're passed that whenever it sees "NSSegmentedCell" to unpack "MyCustomSegmentedCell" instead.  Then you call [super initWithCoder:coder] and swap the archiver back to using the standard NSSegmentedCell.

----
Note that for pre-10.2 nibs the same technique can be applied using NSUnarchiver methods. However I really hope that nobody here is forced to deal with pre-10.2 nibs anymore.
----
David that code worked without a hitch! I didn't find the need to alter the unarchiving methods of the nib. Perfect! Thankyou very much.
----
Is there anyway to achieve varying row heights in the NSBrowser like you can do with NSTableView � tableView:heightOfRow: Does it require the cells to be subclassed?

----
NSBrowser is implemented using a bunch of NSMatrix views. Unfortunately NSMatrix doesn't seem to allow varying row heights, but if you were going to subclass something, that would be it.
----
I decided to use NSTableView, and imitate the behaviour of the browser. It worked out well. Thankyou!

