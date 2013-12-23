---
layout: page
title: SavingAndLoadingUIElements
---

This is my introduction to a UI application builder. Anyway, the project can be downloaded from http://homepage.mac.com/lichach/OvalsForYou.zip  (link is no longer valid)

If you take a look at it some of you may recognize it from a challenge in Aaron Hillegass' Cocoa programming book. I can't figure out how to load a sub-class of NSView that is an outlet on the .nib file. What seems to be happening is that no matter what instance the application creates I have no influence over it. The problem is that I have already saved a NSView subclass called OvalView into a file. Now when I read from that file I can't seem to get the data onto the UI. I save it okay, I load it okay, but I can't seem to get the object to display as the IBOutlet that I have pointed to. 

Any help on this subject would be greatly appreciated. I've been stuck on this for a day or so and I can't seem to figure out what I'm doing wrong. If you could either point out my error, or point to where in the book it is located I would gladly look up the necessary procedure to go about fixing this.

Thank you for your time,
Brett (A cocoa newbie)

----

I looked at your source. You need to instanciate your OvalView in InterfaceBuilder: click on the classes tab, navigate to NSView>OvalView, select it, control-click and choose Instanciate OvalView in the contextual menu. Once done, you can see a custom view icon named OvalView in the instances tab.

PS: clean your target (shift-cmd-K or delete the Build folder that�s in your project folder) before putting your sources on line for download or the file will be very big (18,6Mb, 4,6 zipped; only 176kb, 32kb zipped once cleaned!!!).

--wdyp

----

Thanks for your help, but it seems to contradict the information that was presented to me in the book. In the book it says to drag a customView UI element onto the window and then custom class(command-5) it to make the view an instance of OvalView. I also tried it your way, but the problem I have is trying to get that instantiated instance be the instance of the class that I want to use to display. If you could provide further clarification/assistance I would appreciate it.

Thanks for the tip on size saving, it's now done and I will do that in the future. 

Brett

----

I don�t understand this sentence: "I also tried it your way, but the problem I have is trying to get that instantiated instance be the instance of the class that I want to use to display.".

If you want to display your custom view in your window, you just have to drag its .h file from Xcode to IB, intanciate it, then select it as the custom class of the blank custom view you grab from the IB palette. I don�t understand how it is different from what you want.

--wdyp

----

Why are you saying to instantiate OvalView? The instance created by instantiating it will just be wasted memory, since it's not displayed in any window.

----

wdyp: The instance is not a problem, but having access to that instance doesn't change the fact that there is already an instance being created by the .nib startup, right? I'm having the problem of loading an instance from a file and then setting that instance to be the OvalView that I want displayed. 

Does this make more sense?

Thanks,
Brett

----

How about just removing the old view from the window and adding the new view to the window? removeFromSuperview and addSubView should work there.

----

Well thanks for the direction previous poster, but I can't seem to get it to work. All that happens after a load now is that the window will not contain any views at all. This is what I changed, I put it in this function because I think this the the method that should be used for stuff after the document's nib file has been loaded. Unless you recommend me overwriting the awakeFromNib for the document. 

    
- (void)windowControllerDidLoadNib:(NSWindowController *) aController
{
    [super windowControllerDidLoadNib:aController];
    // Add any code here that needs to be executed once the windowController has loaded the document's window.
	if(loadedView) {
		[ovalArea removeFromSuperview];
		ovalArea = loadedView;
		loadedView = nil;
		ovalArea superview] addSubview:ovalArea];
		[ovalArea setNeedsDisplay:YES];
	}
}


Brett

----

When you are calling [ovalArea superview] it will return null, as you have just removed it from the window. Create another outlet for the window and ask for the content view.

----

Ok, now I understand, but unfortunately I don�t know how to do that :( .

--wdyp

----

I (a new poster on this topic) had a look at your code. A "cursor"-y inspection left me suspecting you have
not created any ovals to put into the array (an array of ovals, am I right?)

There is nothing whatsoever wrong with your custom view. And please, Dear God, get rid of all that stuff in your window controller
where you remove from superview and then add it again. That is not the problem. And you're lucky it hasn't become one for you.

Try instantiating some ovals.

In your drawRect: I added this

    
	[[[[NSColor purpleColor] set];
	
	//try this for starters
	curOval = [ NSBezierPath bezierPath ];
	[ curOval moveToPoint: NSMakePoint(100,100) ];
	[ curOval lineToPoint: NSMakePoint(200,200) ];
	[ curOval setLineWidth: 1 ];
	
	[curOval stroke];


to get a diagonal purple line.

----

Previous: Thanks for you hints but I think I am creating ovals. Look at the mouseDown: mouseDragged: mouseUp: addOval: removeOvalAtIndex: inside the OvalView class methods that I have made. The hint that was given to me about removing the view from the superview and then storing a pointer to the window class to add a subview later works. I agree that it probably isn't in the best spot, but I don't have another solution. Could you maybe point me in the direction in which I wouldn't have to do this work around?

Code that is currently working. I figure that I currently don't need to store the window beyond this method so I have just an autorelease variable storing it until I need it later.
    
- (void)windowControllerDidLoadNib:(NSWindowController *) aController
{
    [super windowControllerDidLoadNib:aController];
    // Add any code here that needs to be executed once the windowController has loaded the document's window.
	if(loadedView) {
		NSWindow *theWindow = [ovalArea window];
		[ovalArea removeFromSuperview];
		ovalArea = loadedView;
		loadedView = nil;
		theWindow contentView] addSubview:ovalArea];
		[ovalArea setNeedsDisplay:YES];
	}
}


Brett

----

I can't see why removing items from a superview could be a problem, unless of course you have more than one item in the superview. Cocoa is designed for flexibility, this just shows it.

----

Flexible, fine. But it has nothing to do with the problem, which appears to be tied up in managing memory for the things that are supposed to be drawn by dragging the mouse. I get SIGBUS as soon as I drag in the custom view.

Something is dicey about the Bezier path when it is created in mouseDragged. Its bounds and extent are all zeros.

And that is trapped right on exit from addOval: somehow, there, newOval is offal.

So, yes, you are creating ovals. It's just that they're what math people call "vanishingly small".

----
Really? I don't have the bug on my system, just in case I have uploaded a new project (at the same URL as above) that checks to see if the start point and end point are the same before progressing with the rest of the method. I have also added undo capabilities as per the finishing requirement of the challenge. If you can find a better solution to the view removal and switching I would very much like to hear it.

Brett

----

If you do not see the bug I reported, then perhaps your source code is different from what you made available online.

In no more than 15 or 20 minutes I threw together this custom view that draws a single oval based on mouse events. It is absurd to
conclude that some special manipulation of the subviews is necessary to create a drawing application to the specs of Hillegass' simple challenge.
I left the document class as simply the default given by the project template, and added the custom view to the document window.

The code necessary to create a display list (the equivalent of your [[NSMutableArray (ovals)
and iterate through it each time you create a new oval is not difficult, but I have not included it.
Since (in my version) the bp variable is released-retained each time a new oval is drawn, you can add it to the display list at that point
and run an enumerator across each object in the list at that time. The setNeedsDisplay will take care of drawing them.

I apologize for my missteps in diagnosing your code, but it has so many layers of memory management that it is quite
difficult to determine just where it is breaking down, at least on short notice.

I hope you find the code below more straightforward.

I have a project with similar characteristics that deals with the problem of the display list, although it does not
need to intercept mouse events. It deals with up to 10 or 20 thousand objects in the list.
**
NOTE: For the Interface Builder portion, simply drop your custom view on the document nib file so that OvalView is a known class and change the
custom class of the view that you drag into the window to OvalView using the CustomClass pane of the Info window.
There is no need to create an explicit instance of this view programmatically. I think that notion has added to your confusion about this project.
You "instantiate" a custom view when you drag it into your design window in IB, at least in tutorial projects like this one.
**

    

// OvalView.h
#import <AppKit/AppKit.h>

@interface OvalView: NSView
{
	NSPoint start;		//The starting point of the click
	NSPoint end;		//The ending point of the click
	NSBezierPath *bp;
}

- ( NSRect ) createRect;

@end


// OvalView.m

#import "OvalView.h"

@implementation OvalView

- ( id ) initWithFrame: ( NSRect ) frame
{
	self = [ super initWithFrame: frame ];
	if ( self )
	{
		bp = [ [ NSBezierPath alloc ] init ];
	}
	return self;
}

- ( void ) dealloc
{
	[ bp release ];
	[ super dealloc ];
}

- ( void ) drawRect: ( NSRect ) rect
{
	//First color the background white
	[ [ NSColor whiteColor ] set ];
	[ NSBezierPath fillRect: rect ];
	
	[ [ NSColor blackColor ] set ];
	[ bp setLineWidth: 1 ];    // in case you do not want to fill
	[ bp fill ];
}

- ( void ) mouseDown: ( NSEvent * ) theEvent
{
	NSPoint p = [ theEvent locationInWindow ];
	start = [ self convertPoint: p fromView: nil ];
	//NSLog( @"down at %f %f", p.x, p.y );
	end = start;
}

- ( void ) mouseUp: ( NSEvent * ) theEvent
{
	NSPoint p = [ theEvent locationInWindow ];
	end = [ self convertPoint: p fromView: nil ];
	//NSLog( @"up at %f %f", p.x, p.y );
	[ bp release ];
	bp = [ [ NSBezierPath bezierPathWithOvalInRect: [ self createRect ] ] retain ];
	[ self setNeedsDisplay: YES ];
	 // Not necessary; rect is based only on mouse down and up events
         //end.x = end.y = start.x = start.y = 0;
}

- ( void ) mouseDragged: ( NSEvent * ) theEvent
{
	//First get the new end point
	NSPoint p = [ theEvent locationInWindow ];
	end = [ self convertPoint: p fromView: nil ];
	//NSLog( @"drag at %f %f", p.x, p.y );
	//Only when the user has dragged do we want to redo the current oval
	//If we had a previous oval release it before setting the pointer to a new oval
	
	[ self setNeedsDisplay: YES ];
}

- ( NSRect ) createRect
{
	// We need to find the min of the two points
	float minX = MIN( start.x, end.x );
	float maxX = MAX( start.x, end.x );
	float minY = MIN( start.y, end.y );
	float maxY = MAX( start.y, end.y );
	
	//NSLog( @"rect at %f %f %f %f", start.x, start.y, end.x, end.y );
	
	return NSMakeRect( minX, minY, maxX - minX, maxY - minY );
}

@end



----

Difficulties in restoring the archived objects to the view when a document is loaded into a window likely result from the fact that the
document is unarchived before the window is loaded from the nib. Redisplay the view using the unarchived data in
windowControllerDidLoadNib.

