---
layout: page
title: NSStatusItem
---

http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSStatusItem_Class/index.html

----

The "proper" way of doing an NSMenuExtra.

A Status Item lives in the NSStatusBar (the top right of the screen Menu Bar)

http://www.diggory.net/grazing/vidimenu.gif

They cannot be moved to the right of any NSMenuExtra.

Actually, the above is not true - you can if you use a private API call to create them, and give them a certain value for their "priority". This moves you right over to the right, past all the NSMenuExtra<nowiki/>s. Also if you kill SystemUIServer, it all gets shuffled around and badly screwy. The entire implementation is a bit skewiff, I'm hoping they improved it for Panthwire. -- FinlayDobbie

----

The private API call apparently is

- _statusItemWithLength:(float)fp40 withPriority:(int)fp16;

It looks like any value above 1000 will give you a NSStatusItem that floats to the right of the NSMenuExtra<nowiki/>s... can anyone verify that I'm right? --JacobKaplanMoss

Also: How do you switch off the drop shadow that is applied automatically?

-- AndreasMayer

----

N.B. the NSStatusBar does *not* retain the status item - so to keep one of these around while your app is not running, you'll need a background app.
----
Note: With Panther, Apple removed the drop shadow from NSStatusItems. Problem solved. :}

They still can't be moved like menu extras though - and the private API call to select a certain priority seems not to be working anymore.

----

**Discussion**

Does anyone know how to receive a right click event from a StatusItem? I have tried having a view with subclassed buttons and a subclassed view, but I can only receive left click events.

A view will receive all events as expected. What you are probably seeing is the ctrl action acting like a left click. In other words, you will have to capture the control and act like it was a right click. Quite strange behaviour, but I am sure there are reasons.

----
Sorry the private API call doesn't work. I tried it and it giave me a warning: `NSStatusItem' does not respond to `withPriority:'

----

It's quite probably been deprecated. Apple is very annoyed at developers shifting things around in the menubar for some unbeknownst reason. You might want to have a look at the mailing lists to see if anything has appeared lately.

----
Worked with it, got help from the guy who made Menu Calendar http://www.versiontracker.com/dyn/moreinfo/macosx/11903&mode=feedback and I got it to work. NSMenuExtras are to hard to work with (for me anyway)
----
I'm writing a NSStatusItem, and I have this code to populate the NSMenu with items
    
    int i;
    NSArray *aFrameworks = [[NSFileManager defaultManager] directoryContentsAtPath:@"/System/Library/Frameworks/"];
    while ([frameworksMenu numberOfItems]) 
    for(i=0; i<[aFrameworks count]; i++) 
    {
        NSString *aName = [aFrameworks objectAtIndex:i];
        NSMenuItem *aItem = [frameworksMenu addItemWithTitle:[aFrameworks objectAtIndex:i]];
    }

And it never inserts the items? Can you help.

----
just use a nib file...
----

*You can't use a nib file...the menu's dynamic. However, I think the problem is in your     while loop. Basically you are testing whether or not your frameworks menu has items. For one thing, this doesn't seem necessary at all: the     for-loop seems to work perfectly fine to add items. But in case another test was necessary, I don't think     numberOfItems was the one you wanted. Also, it's possible (but unlikely) that frameworksMenu is an invalid pointer...I've had that happen before. --JediKnil*
----

How about capturing keystrokes when the NSStatusItem's view is a NSSearchField?  I just whacked a search field into a window in IB, and hooked an outlet up to it - then in the awakeFromNib I set the status item's view to this outlet.  The search field appears in the status bar, but I can't send keystrokes to it.  I can left-click and double-left-click then even drag default search field text around with the text field...

Frustrating... anyone able to point me in the right direction?
--johnclayton

----

Beginning in 10.3, NSStatusItem provides a     -(void)popUpStatusItemMenu:(NSMenu *)theMenu method to show a menu in a view-based status item at the appropriate location.

From the 10.3 Cocoa release notes:

*

For standard status bar items, you can now set an alternate image that is displayed when the item is highlighted on mouse tracking.

- (void)setAlternateImage:(NSImage*)image;

- (NSImage*)alternateImage;

For custom view status bar items, there are two new methods to help emulate standard items. The first method will draw the menu background pattern in the status item custom view in regular or highlight pattern.

- (void)drawStatusBarBackgroundInRect:(NSRect)rect withHighlight:(BOOL)highlight;

This will display a menu under the custom status item.

- (void)popUpStatusItemMenu:(NSMenu*)menu;
*
----

Would it be possible to do a 3rd party host app for NSStatusItems? A la ControlStrip? Anyone interested?

----

You can still use the <code>_statusItemWithLength:withPriority:</code> method to create a NSStatusItem on the right of the NSMenuExtra<nowiki/>s.

You must supply INT_MAX as the priority to achieve this.

However, the status item behaves strangely.

*First, the length parameter does not appear to be used (the actual length is random).

*Second, moving between the status item and the NSMenuExtra<nowiki/>s is flawed.

Clicking on an NSMenuExtra and moving to your status item will not activate it.

Clicking first on your status item then moving away from it will work, but you can't move back.

*Clicking in your menu item will lock the mouse tracking to the menubar until you click the mouse button again.

If you launch your application, click on your status item, then quit your application -- the mouse will still drop down menus for NSMenuExtra<nowiki/>s as it moves over them.

Here is my code:

    
NSStatusItem * CreateStatusItem ( void )
{
    NSStatusItem * statusItem ;

    if( statusItem =  [[NSStatusBar systemStatusBar ] _statusItemWithLength:0 withPriority:INT_MAX ] )
    {
        [ statusItem setLength:0 ];

        //  Setup the status item here.

        [ statusItem setLength:NSVariableStatusItemLength ];
    }

    return statusItem ;
}


The reason I set the length to zero before setting up the status item is <code>_statusItemWithLength:withPriority:</code> gives a random length. When you set the title, image, or view it will appear with the random length in the menubar. This will look strange as your status item may appear truncated or oversized and the NSMenuExtra<nowiki/>s will move twice (once with the random length and again when you set the desired length).

-- elfarr

----

Well, crap. I could be wrong, but it would appear that this no longer works. I�m trying it under 10.6.3; the code works, and the private API still exists; even passing <code>0</code>, which I thought might cause it to crash and burn, *works*� it just appears in the place it normally would, instead of where I want it to.

I�m trying to build a replacement clock, and it appearing on the left of the menubar is completely unacceptable� but I don�t really see any way around it, right now. That�s really pissing me off.

-- ElliottCable: http://elliottcable.name/

----

I've created an instance NSStatusItem,     statusItem, and I'd like it to be able to receive drop (as in DragAndDrop) events. So, I created a subclass of NSView,     DropView, and set it as a subview of the NSStatusItem's     view, using the following code:
        DropView *aDropView = [[DropView alloc] initWithFrame:statusItem view] frame;
    statusItem view] addSubview:aDropView];

*If you want your new [[DropView to fill the statusItem's view, you should be using its bounds as the DropView's frame.*

In the     initWithFrame: method of DropView, I register for a file to be dropped:
    - (id)initWithFrame:(NSRect)frameRect {
	// Prepare for drag and drop
	NSArray *draggedTypeArray = [NSArray arrayWithObjects:NSFilenamesPboardType, nil];
	[self registerForDraggedTypes:draggedTypeArray];
	
	return self;
}

Yet I get no feedback when I drag a file across the NSStatusItem, even though it should print out a message:
    - (NSDragOperation)draggingEntered:(id <NSDraggingInfo>)sender {
    NSLog(@"draggingEntered:");
    return NSDragOperationNone;
}

Any idea what I could be doing wrong?

-- RyanGovostes

----

My guess is that NSStatusItems cannot support drag and drop. In fact, they do not support many things on different builds of Mac OS X with regards to views.

----

I got around this by using a custom NSView instead of the default NSStatusItemView. It works perfectly now. -- RyanGovostes

----

Can anyone help me figure out how I can determine the location of my NSStatusItem on the screen? I've tried to get the origin of the custom view's window, but it seems like it's offscreen (even though Quartz Debug tells me it's not), because it reports an origin of (-40,0) (where 40 is the length of the status item).

----

The following works for me in an NSStatusItem with a custom view, where 'self' below is the view of the NSStatusItem:
        NSPoint globalOrigin = self window] convertBaseToScreen: [[NSMakePoint(0, 0)]; 

----

Hi, I have a NSStatusItem with a menu containing an NSMenuItem. When that menu item is clicked, a window is popped up, but it doesn't seem to be 'active' even though i tell it to make key and order front.

Here is my code for popping up the window:
     [prefWindow makeKeyAndOrderFront:self]; 

Also, when my app starts, i set the level of my window to be above other windows:
     [prefWindow makeKeyAndOrderFront:self]; 

This 'non-activated' window is also happening even with NSRunAlertPanel

Any ideas why this is happening???

----

Your app isn't key because it's a semi-background process (with a UI).  In order to have your windows actually be key, you should call [NSApp activateIgnoringOtherApps:YES] before ordering front a window.  There may be other methods, but this works for me.

----

Thanks! That works perfectly, and i appreciate the explanation also.

----

A note to those using garbage collection....be sure to keep a reference to the NSStatusItem you create (rather than simply retaining it) or it will be collected and disappear from the menubar. I create and keep a reference from within my app delegate.

