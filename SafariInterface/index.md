---
layout: page
title: SafariInterface
---



I'd like to create buttons that behave like those nav buttons in Safari. What I do like: when I press and hold the mouse button on that button, a contextual menu would appear after some seconds. I've scanned through the AppKit objects and found NSSegmentedControl, but that object doesn't afford such opportunity.

----

This is where you would subclass the NSSegmentedControl and add the desired functionality. In this case, you might want to override mouseDown and attach a timer or something to fire the menu, remembering to clear the timer when it either fires or the mouse is released before it's able to fire.

----

Unfortunately by using NSSegmentedControl's subclass, I didn't manage to reach this behavior. I tried a bunch of possible solutions, but I couldn't figure out where or when it calls the menu showing function.

----

Well, obviously you can use SetMenu:ForSegment: to set up a menu...
I don't know if this would work, but couldn't you send a delayed message to the segmented control to set up the menu, then the menu will come up since the mouse is down... If the mouse is released you can just cancel the delayed message. I don't know if setting up the menu during the click is a good idea though, and you'd have to have a seperate menu that's blank to switch to. Or you could subclass NSMenu and have a menu that won't display for a certain amount of time... -RossLeonardy

----

Well, if you open Safari's main NIB, you will find that the all the buttons are just that, NSButtons. They dont use NSSegmentedConrol control to do the buttons. So it's as simple as setting a menu for the button.

Jacob

*Actually, the forward and back buttons are of class "ButtonWithMenu", a custom subclass of NSButton.  This is in Safari 1.2.3.*

----

On a related topic: See entry dated 2004.10.04 in AppKitMostWanted.

----

Using NSButton's subclass I've implemented the bigger part of the described behavior. Here's the code:

    

#import "MyButton.h"

@implementation MyButton

- (void)displayMenu:(NSTimer *)theTimer
{
	NSEvent *theEvent=[timer userInfo];
	NSEvent *click = [NSEvent mouseEventWithType:[theEvent type] location:[self frame].origin modifierFlags:[theEvent modifierFlags] timestamp:[theEvent timestamp] windowNumber:[theEvent windowNumber] context:[theEvent context] eventNumber:[theEvent eventNumber] clickCount:[theEvent clickCount] pressure:[theEvent pressure]]; 
	[NSMenu popUpContextMenu:[self menu] withEvent:click forView:self];
	[timer invalidate];
	[self highlight:NO];
}   

- (void)mouseDown:(NSEvent *)theEvent 
{ 
	[self highlight:YES]; 
	timer=[NSTimer timerWithTimeInterval:1.0 target:self selector:@selector(displayMenu:) userInfo:theEvent repeats:NO];
	[[NSRunLoop currentRunLoop] addTimer:timer forMode:@"NSDefaultRunLoopMode"];	
} 

- (void)mouseUp:(NSEvent *)theEvent 
{ 
	[self highlight:NO];
	[timer invalidate];
	[self sendAction:@selector(back:) to:[self target]];
} 


@end



----

I was looking at Safari's nib files, and I came upon something that I personally think is very interesting, in the Bookmark nib file, there are two sub-classes.  One is called OutlineViewPlus, and the other called TableViewPlus; any idea what these classes are?

----

They're just subclasses of the outline view and table view classes. I imagine TableViewPlus handles gradient drawing for selections, while OutlineViewPlus was used to draw the striped rows pre-Panther.  There is probably a lot of additional functionality added to the sub classes, i.e. drag'n'drop support, easy image (label) support etc.

----

I have another question about the SafariInterface that has been bothering me for sometime.  Safari has a textured window, but the resize corner is the size of a regular window. How did Apple do that?

----

The resize corner is achieved using the NSView subclass BarBackground. That is also how they do the 90� corners.
Actually, I think that the corner is done with the private NSWindow method setBottomCornerRounded:. --RobinHP

