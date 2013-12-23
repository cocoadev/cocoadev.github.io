---
layout: page
title: SubclassingNSButtonCell
---

I'm trying to clear up a lack of understanding that I clearly have about subclassing.

I have an General/NSMatrix of General/NSButtonCells already set up.  Works fine - as expected.
Now I want to change the behavior (a new mouse over behavior, actually).  So I tried to subclass General/NSButtonCell and test by overriding mouseDown: by creating 
    
/* General/MyButtonCell */

#import <Cocoa/Cocoa.h>

@interface General/MyButtonCell : General/NSButtonCell
{
}
@end



#import "General/MyButtonCell.h"

@implementation General/MyButtonCell
- (void)mouseDown:(General/NSEvent *)theEvent
{
	General/NSLog(@"General/MYMouseDown!");
	//	[super mouseDown: theEvent];
}
@end


In IB I set the various General/NSButtonCell items to my custom class of General/MYButtonCell.
However when run, the check boxes exhibit the same behavior as before.  Where am I going wrong?

Thanks

----

General/NSCell (and thus General/NSButtonCell) is not a subclass of General/NSResponder, and so it doesn't use     mouseDown: or any other General/NSResponder messages. Look at the documentation/header for General/NSCell to see which methods General/NSCell uses to detect mouse clicks.

----

Never mind - I'm an idiot.  I'm subclassing General/NSMatrix, which, in fact, actually *has* a mouseDown: method.

----

If you'd rather subclass General/NSButtonCell, take a look at the mouse tracking methods.     -General/[NSCell startTrackingAt:inView:],     -General/[NSCell trackMouse:inRect:ofView:untilMouseUp:], etc.

----

I got too cocky after I figured out how to override the mouseDown: method of the General/NSMatrix.
Now I can't figure out how to handle the mouseEntered:/mouseExited methods of my individual General/NSButtonCells in the General/NSMatrix.  I subclassed General/NSButtonCell and tried to simply override those two methods, however it doesn't seem to be working.  What am I not understanding?

----
Again, you're hitting the General/NSCell/General/NSControl problem. You should really check out http://developer.apple.com/documentation/Cocoa/Conceptual/General/ControlCell/index.html and take a look at General/NSCell's documentation. However, don't take this as RTFM...I used to have a lot of the same problems as you. Basically, cells handle stuff differently than views (or responders in general), so you have to use the General/NSCell versions of the responder methods. --General/JediKnil

----
I really appreciate the advice.  And RTFM isn't an awful suggestion  :-)  but I guess there are some fundamental concepts that I still haven't gotten a hold of.  General/NSButtonCell inherits from General/NSCell, right?  Perhaps I don't know understand what mouseEntered:/mouseExited: does.  Guess it's time for me to RTFM.
----
Ok, I'm trying to get a handle on startTrackingAt:inView: but it only seems to be called when the mouse is in the control AND down.  Am I wrong?  If not, how can I track the mouse when the mouse isn't down.

I can read the documentation, but the likes of me really need examples sometimes.
----
General/NSTableViewRollover might work for an example.

----
Not to take away from your problem but General/CCDColoredButtonCell could use a pair of skilled eyes. I've been having trouble getting it working as a dataCell in an General/NSTableView - it only draws in the first row and I plain can't figure out why.

----

**Be sure to retain your private instance variables when subclassing**  (formerly titled General/ImplementingNSCopying, but that wasn't the problem)

I was subclassing General/NSActionCell, which seemed to require me to adopt General/NSCopying in the subclass otherwise things fail when my subclass is used in a table view. Initially I did so naively, just creating a new instance, setting instance variables and continuing. I realized soon enough that this was probably wrong, since I wasn't setting all the ivars, just my additions. So I changed it to send copyWithZone: to super, but that didn't work at all. The cells disappeared as the table view (column) copied them but the copies ... failed. I got a complaint when debugging that General/NSCFType does not respond to -drawWithFrame:inView messages.

The docs on implementing General/NSCopying told me that my strategy (alloc/int, General/NSCopyObject() or call super's implementation) depends on the implementation of the superclass. ??? I don't *know* how General/NSActionCell implements -copyWithZone:.

I was wondering whether this has something to do with subclassing Apple classes which adopt General/NSCopying. But the solution turned out to be:  I wasn't properly assigning the new private instance variables in the subclass, so they weren't being retained.
