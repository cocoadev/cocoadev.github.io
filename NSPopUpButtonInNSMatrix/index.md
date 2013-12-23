---
layout: page
title: NSPopUpButtonInNSMatrix
---



I'm trying to make a NSPanel as a sheet that appears when a new NSWindow is opened. I want this sheet to allow n-number of rows of NSControl<nowiki/>s to be generated to collect information from the user. I want each row to contain an NSImageView, an NSTextField, and an NSPopUpButton. Think of how iConquer's ( http://www.kavasoft.com/iConquer/tour/index.php?lang=en&page=1 ) player name collection dialog looks for an example...

I originally thought about using an NSTableView, but I couldn't figure out how to make it transparent. (It always drew a solid color behind it.)

I've been trying to use parallel NSMatrix<nowiki/>s by option-dragging in InterfaceBuilder, and using the YSpacing to line the rows up. While I can make multiple NSImageView<nowiki/>s and NSTextField<nowiki/>s, IB simply beeps when trying to drag the pop-up. At first, I suspected that this class isn't implemented using NSCell<nowiki/>s, but both IB's class browser and AppKiDo are reporting that there is a NSPopUpButtonCell implemented. I also tried using NSComboBox<nowiki/>es, which do matrix, but they don't work the same. (They don't support MenuSeparators, and they have text selection behavior I don't want.)

I haven't been doing much Cocoa development for long, but this is making me wonder if I have to hard-code some functionality instead of GettingItForFree. I've tried searching through Apple's mailing list archives, but only found one other reference to this problem, and nobody provided assistance to the person who brought it up. Anybody successfully solved this problem?

-- DLWormwood



----
Actually, you do use NSTableView. See this: http://www.stepwise.com/Articles/Technical/2003-12-20.01.html

You'll need to set the NSOutlineView's background color to [NSColor clearColor], as well as the NSTableView (the table view is inside the outline view).

If you don't want to go through this, you'll need to be creating new controls. e.g. NSButton *button = [[NSButton alloc] initWithFrame:rect]; set all you need (like style, etc.) then, use panel contentView] addSubview:button]; to add the control to the view. Remember that the control is not autoreleased, you need to release it on dealloc.

----

The example you linked to seems to be overkill for my needs.  [[NSTableView's cells provide the controls I need without inventing my own NSView<nowiki/>s to embed in each row.

Also, did you mean NSScrollView rather than NSOutlineView for the NSTableView's container?  I added the following to my code to try to make the table transparent...

    
[myTable setBackgroundColor:[NSColor clearColor]];
myTable superview] setBackgroundColor:[[[NSColor clearColor]];
myTable superview] display];


...but the table gets a solid black background, just like if I set a 0% opacity color in IB. I also get a compiler warning, but I can insert a typecast or store the superview in a temp variable to get around that. I also have to double-click on the text field to edit it, since I'm working on disabling row selection behavior, which I don't need.

Making a wrapper data source to process an [[NSImage, NSString and NSNumber (tabled pop-ups need an index, it seems) appears to work otherwise. I'll have to make a dictionary inside the data source or somesuch rather than checking control values by tag at panel close...

-- DLWormwood


----

I did mean NSScrollView. In Interface Builder, select the Classes tab, then select the table view in your window. NSTableView is a view inside a NSScrollView. NSOutlineView is a subclass of NSTableView.

Now it remains a mystery why you got your backround black. Try NSScrollView's - (void)setDrawsBackground:(BOOL)flag to NO, so it doesn't draw the background at all (then it should be transparent).

I recommend a little app called Cocoa Browser - you can easily access all classes and see the hierarchy.

-- Charlie

----
Note that     [tableView superview] won't get you the NSScrollView, it'll get you the scroll view's NSClipView, which is probably why this isn't working. To get your enclosing scroll view, the     -enclosingScrollView method should be helpful.

----

I mentioned that I use AppKiDo; it has some of the same functionality.  That's how I caught the NSScrollView ref in the first place.

As for the     -enclosingScrollView call, my code is now up to this...

    
NSScrollView* tableWrapper;
[myTable setBackgroundColor:[NSColor clearColor]];
tableWrapper = [myTable enclosingScrollView];
[tableWrapper setBackgroundColor:[NSColor clearColor]];
[tableWrapper setDrawsBackground:NO];
[tableWrapper display];


...and it still draws a solid black background.

For the record, I'm getting this in a minimal skeleton project:

http://web.mac.com/wormwood/CocoaDev/NSTableViewVersion.gif

While I'm shooting for this, just in case the iConquer reference wasn't clear enough:

http://web.mac.com/wormwood/CocoaDev/InterfaceBuilderHard2Version.gif

The above example was hard designed in IB with two rows.

I'm going to experiment with the stuff at HelpWithCopyingNSView since this seems to be drifting from the original page topic...

----

Why don't you do what Apple does for predicate editors and create, in a separate nib, an NSView with placeholder versions of your controls?  Hook them up to an NSOwner outlet using Bindings or however you wish to get data into the view, and then programmatically add and remove instances of this view as subviews of an NSScrollView.

