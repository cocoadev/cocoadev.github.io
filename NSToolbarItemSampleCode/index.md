---
layout: page
title: NSToolbarItemSampleCode
---

The following is derived from a thread on the MacOSX-dev mailing list run by TheOmniGroup.

Authors include Dave Clark and Michael B. Johnson, the sample code was provided by Chuck Pisula.

To create an NSToolbarItem with a custom view, you have to do a little more work than for the regular NSToolbarItem.
**[ED: Somebody add sample code for that before this section, please!] **
You can create an NSToolbarItem and call setView on it with a custom view. The problem is that the view's drawRect method is never called unless you also implement a few more methods, specifically these: 

    <pre>
- (id) initWithCoder:(NSCoder *)coder;
- (void) encodeWithCoder: (NSCoder *)coder;
</pre>

You also have to make sure the toolbar item knows the view's min/max size. Without this, you'll be allocated a rect that is 0 by 0.

Here is some sample code:

**In your controller: **

    <pre>
- (NSToolbarItem *) toolbar: (NSToolbar *)toolbar itemForItemIdentifier: 
(NSString *) itemIdent willBeInsertedIntoToolbar:(BOOL) willBeInserted {
     // Required delegate method   Given an item identifier, self method 
returns an item
     // The toolbar will use self method to obtain toolbar items that can 
be displayed in the customization sheet, or in the toolbar itself
     NSToolbarItem *toolbarItem = [NSToolbarItem] alloc] 
initWithItemIdentifier: itemIdent] autorelease];

     if ([itemIdent isEqual: SquareDotViewItemIdentifier]) {
	SquareDotView *dv = [[[SquareDotView] alloc] initWithFrame: 
NSMakeRect(0,0,100,32)];
	
	[toolbarItem setView: dv];

	// set a reasonable minimum size
	[toolbarItem setMinSize: NSMakeSize(100,32)];
	// set a maximum size that allows us to stretch.
	[toolbarItem setMaxSize: NSMakeSize(300,32)];

	[toolbarItem setLabel: @"Dot View"];
	[toolbarItem setPaletteLabel: @"A Dot View"];
	[toolbarItem setToolTip: @"This is a dot view"];
     }
   .....
}
</pre>

**The "[[SquareDotView" class: **

    <pre>
@interface SquareDotView : NSView {
@private
     float	sdSide;
     NSPoint	sdLocation;
}
@end


@implementation SquareDotView

#define START_SIDE 	10
#define START_LOCATION 	NSMakePoint(10,10)

- (id)initWithFrame:(NSRect)frame {
     self = [super initWithFrame:frame];
     if (self) {
	sdSide = START_SIDE;
	sdLocation = START_LOCATION;
     }
     return self;
}

- (void)mouseDown:(NSEvent *)theEvent {
     sdLocation = [self convertPoint: [theEvent locationInWindow] 
fromView: nil];
     [self setNeedsDisplay: YES];
}

- (void)drawRect:(NSRect)rect {
     NSColor redColor] set];
     [[NSBezierPath bezierPathWithRect: NSMakeRect(sdLocation.x, 
sdLocation.y, sdSide, sdSide)] fill];

     [[NSColor blackColor] set];
     [[NSBezierPath bezierPathWithRect: [self bounds stroke];
}

- (id)initWithCoder:(NSCoder *)coder {
     self = [super initWithCoder: coder];
     if (self) {
	[coder decodeValuesOfObjCTypes: "fff", &sdSide, &sdLocation.x, 
&sdLocation.y];
     }
     return self;
}

- (void)encodeWithCoder:(NSCoder *)coder {
     [super encodeWithCoder: coder];
     if (self) {
	[coder encodeValuesOfObjCTypes: "fff", &sdSide, &sdLocation.x, 
&sdLocation.y];
     }
}

@end
</pre>

----

Read the documentation on NSToolbar and NSToolbarItem. Makes light work of everything, from attach a toolbar to the window to adding toolbaritems to it. This discussion is on the more technical aspects of toolbaritems and therefore it needs some source code that you may not understand.

I would suggest NSToolbarSampleCode and this link:

http://macdevcenter.com/pub/a/mac/2002/03/15/cocoa.html

----

I wanted to put a progress indicator in the toolbar the app described in ValidatingToolbarItemsInThreadedApp.

I didn't see any existing source code examples, and so I publish the following...

I declared an instance variable in the threaded class (which also happens to be the view class)

    NSProgressIndicator *prog;

In the     init method of the threaded class, I created the progress indicator with the following:

    <pre>
		prog = [ [ NSProgressIndicator alloc ] initWithFrame: NSMakeRect( 0, 0, 60, 16 ) ];
		[ prog setStyle: NSProgressIndicatorBarStyle ];
		[ prog setIndeterminate: NO ];
		[ prog setMinValue: 0 ];
		[ prog setMaxValue: 1 ];
		[ prog setUsesThreadedAnimation: YES ];
</pre>

In the controller class, I added an item to the list of toolbar items and added it to the toolbar with the following,

in 

    <pre>
- (NSToolbarItem *) toolbar: (NSToolbar *) toolbar
	itemForItemIdentifier: (NSString *) itemIdentifier willBeInsertedIntoToolbar: (BOOL) flag
</pre>

put the following block:

    <pre>
	else if ( [ itemIdentifier isEqualToString: @"Progress" ] )
	{
		[ item setMinSize: NSMakeSize( 60, 16 ) ];
		[ item setMaxSize: NSMakeSize( 60, 16 ) ];
		[ item setLabel: @"% Complete" ];
		[ item setPaletteLabel: [ item label ] ];
		[ item setView: [ threadedClass prog ] ];
	}
</pre>

In the place where the progress is happening ( in the main thread, when data are received back from the generator thread ) I have the following

    <pre>
		pct = [ displayList count ] / max;      // Display list contains the drawing instructions for the view class in a mutable array
		
		[ prog setDoubleValue: pct ];
</pre>

Works like a charm.

----

**Debugging a toolbar item with a custom view**

I'm working on a WebKit-based browser and I recently implemented a toolbar. I have several buttons in and working, but I can't get a URL and Google field to work. At runtime it acknowledges its existence by showing its text label but it won't show the fields themselves. All needed IB connections have been made as well as defining the NSView's outlets.

    <pre>

	} else if ( [itemIdentifier isEqualToString:@"netFields"] ) {
	
	[item setLabel:@""];
	[item setPaletteLabel:@"URL Bar and Google search"];
	[item setView: toolbarExtras];
	[item setMinSize:NSMakeSize(30, NSHeight([toolbarExtras frame]))];
	[item setMaxSize:NSMakeSize(400,NSWidth([toolbarExtras frame]))]; 
	}

</pre>

----

you need to set the minSize and maxSize properties of the toolbar item, or else the view won't show up.

what happens if you set the y value for the maxSize using NSHeight?

I don't know what to tell you - I have almost this exact same code working fine. Where is     toolbarExtras coming from? How are the autosizing properties of the view set in IB?

----

Is your NSView correctly initalized? or connected to its IB counterpart? Try NSLog on the object to see if it's nil.

----

AutoSize options are default.

----

*try setting the inner horizontal & vertical springs so your view will resize itself. it may simply be getting resized out of its bounds when your window is displayed*

*try     [[view alloc] initWithFrame:frameRect]. if your view is set up in IB, though, you shouldn't need to do this.*

Change the line that says     [item setMaxSize:NSMakeSize(400,NSWidth([toolbarExtras frame]))];  to this:     [item setMaxSize:NSMakeSize(400,NS**Height**([toolbarExtras frame]))];  -- Devin Lane

