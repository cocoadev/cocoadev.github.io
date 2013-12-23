---
layout: page
title: TwoPDFViewsWithSynchronizedScrolling
---



I'm trying to synchronize the scrolling of two General/PDFViews.

I have created a subclass called, "General/SynchroScrollView" which was described in the Scroll View Programming Guide. http://developer.apple.com/documentation/Cocoa/Conceptual/General/NSScrollViewGuide/index.html

My problem is I can't figure out how to swap the General/NSScroller(s) that currently come as subviews of the General/PDFView class with the General/SynchroScrollView instances. I'd rather not build a General/PDFViewer from scratch, but if I have to, I guess I'll have to.

Some info:
My window's contentView includes a  General/PDFView as well as some other outlets.
The General/PDFView's subviews are two General/NSScrollViews (I don't know why there are two...for vertical and horizontal is all I can guess);
Each General/NSScrollView has an General/NSClipView as a subview and each General/NSClipView has a General/PDFMatteView.


Also, I'm happy to hear any other solutions to the synchronization problem if you have them.

Thank you.
----
You could try switching to the hierarchial view in IB, selecting the General/NSScrollView<nowiki/>s, and then using the inspector panel, choosing a custom class, i.e. your S<nowiki/>ynchroScrollView. Should work. --General/JediKnil

----
Thanks General/JediKnil, I've never used hierarchical view before and I'm glad you suggested it because now I know about it and it looks like a pretty valuable tool. That being said, when I looked at General/PDFView within the hierarchical view, there were no disclosure triangles to suggest that there were subviews for General/PDFView...maybe I'm missing something...any other ideas?
-General/EricB

----
Oops...sorry...I assumed General/PDFView was like the other scrollable views in IB. I guess it needs a more specific way to scroll, with the pages and all. OK, I take back what I said. So, sorry, but I haven't worked with the PDF Kit before. So I'll offer one risky suggestion...make ALL scroll views that come out of a nib S<nowiki/>ynchroScrollViews, using     setClass:forClassName:. Then connect the scroll views yourself, which itself is risky because you can't get at them besides getting the subviews. But probably you should wait for someone better with PDF Kit to come along. And Apple does say, on the General/PDFView page, that "*you can also create a custom PDF viewer by using the PDF Kit utility classes directly and not using General/PDFView at all.*" --General/JediKnil

----
Another enlightening recommendation...I've learned alot by following through on it though I still can't get the General/PDFView instances subviews (normally General/NSScrollView) to actually be General/SyncroScrollView instances with this method... I'll keep trying though I'm still open to other solutions...thanks again General/JediKnil.

----
Well, now the two General/PDFView subclass instance subviews are showing up as General/SynchroScrollView so at least that's good...I used:

    
- (id) initWithCoder: (General/NSCoder *) decoder
{
        BOOL useSetClass = NO;
        BOOL useDecodeClassName = NO;
	
        if ( [decoder respondsToSelector: @selector
		(setClass:forClassName:)] ) {
		useSetClass = YES;
		[(General/NSKeyedUnarchiver *) decoder setClass: General/[SynchroScrollView class] forClassName: @"General/NSScrollView"];
		
	}
        else if ( [decoder respondsToSelector: @selector
		(decodeClassName:asClassName:)] ) {
		useDecodeClassName = YES;
		[(General/NSUnarchiver *) decoder decodeClassName: @"General/NSScrollView" asClassName: @"General/SynchroScrollView"];
	}
	
        self = [super initWithCoder: decoder];
	
         if ( useSetClass ) {
		[(General/NSKeyedUnarchiver *) decoder setClass: General/[SynchroScrollView class] forClassName: @"General/NSScrollView"];
	}
	else if ( useDecodeClassName ) {
		[(General/NSUnarchiver *) decoder decodeClassName: @"General/NSScrollView" asClassName: @"General/SynchroScrollView"];
	}
        return self;
}


The only thing I don't understand - the output from     General/NSLog(@"General/ScrollViews %@", General/self pdfView] ) is "    2007-01-02 11:49:01.848 [[RealEyes[5866] General/ScrollViews (<General/SynchroScrollView: 0x37a420>, <General/NSScrollView: 0x5215f00>)"...why aren't they both General/SynchroScrollView?

----
*Your code was all brokenly formatted, I fixed it up. See General/TextFormattingRules for info on how to make your code show up properly.

----
After creating my own General/PDFKitViewer, I came up with a more elegant solution, adding a General/SynchroScrollView category to General/NSScrollView. My code is below. - General/EricBadros

    
#import <Appkit/General/NSScrollView.h>
@interface General/NSScrollView (General/SynchroScrollView)

	General/NSScrollView* synchronizedScrollView; // not retained, global
	


- (void)setSynchronizedScrollView:(General/NSScrollView*)scrollview;
- (void)stopSynchronizing;
- (void)synchronizedViewContentBoundsDidChange:(General/NSNotification *)notification;
@end

@implementation General/NSScrollView (General/SynchroScrollView)

	- (void)setSynchronizedScrollView:(General/NSScrollView*)scrollview
	{
		General/NSView *synchronizedContentView;
		
		// stop an existing scroll view synchronizing
		[self stopSynchronizing];
		
		// don't retain the watched view, because we assume that it will
		// be retained by the view hierarchy for as long as we're around.
		synchronizedScrollView = scrollview;
		
		// get the content view of the 
		synchronizedContentView=[synchronizedScrollView contentView];
		
		// Make sure the watched view is sending bounds changed
		// notifications (which is probably does anyway, but calling
		// this again won't hurt).
		[synchronizedContentView setPostsBoundsChangedNotifications:YES];
		
		// a register for those notifications on the synchronized content view.
		General/[[NSNotificationCenter defaultCenter] addObserver:self
                                                                           selector:@selector(synchronizedViewContentBoundsDidChange:)
				                                              name:General/NSViewBoundsDidChangeNotification
							                     object:synchronizedContentView];
		
	}
	
	- (void)synchronizedViewContentBoundsDidChange:(General/NSNotification *)notification
	{
		// get the changed content view from the notification
		General/NSView *changedContentView=[notification object];
		
		// get the origin of the General/NSClipView of the scroll view that
		// we're watching
		General/NSPoint changedBoundsOrigin = [changedContentView bounds].origin;
		
		// get our current origin
		General/NSPoint curOffset = General/self contentView] bounds].origin;
		[[NSPoint newOffset = curOffset;
		
		// scrolling is synchronized in the vertical plane
		// so only modify the y component of the offset
		newOffset.y = changedBoundsOrigin.y;
		
		// if our synced position is different from our current
		// position, reposition our content view
		if (!General/NSEqualPoints(curOffset, changedBoundsOrigin))
			{
			// note that a scroll view watching this one will
			// get notified here
			General/self contentView] scrollToPoint:newOffset];
			
			// we have to tell the [[NSScrollView to update its
			// scrollers
			[self reflectScrolledClipView:[self contentView]];
			}
	}
	
	- (void)stopSynchronizing
	{
		if (synchronizedScrollView != nil) {
			General/NSView* synchronizedContentView = [synchronizedScrollView contentView];
			// remove any existing notification registration		
			General/[[NSNotificationCenter defaultCenter] removeObserver:self	name:General/NSViewBoundsDidChangeNotification object:synchronizedContentView];
			
			// set synchronizedScrollView to nil
			synchronizedScrollView=nil;
		}
		
	}
+ (General/NSCursor *) dragCursor
{
    static General/NSCursor  *openHandCursor = nil;
	
    if (openHandCursor == nil)
		{
        General/NSImage    *image;
        image = General/[NSImage imageNamed: @"fingerCursor"];
        openHandCursor = General/[[NSCursor alloc] initWithImage: image hotSpot: General/NSMakePoint (8, 8)]; // guess that the center is good
		}
	
    return openHandCursor;
}



//  canScroll -- Return YES if the user could scroll.
- (BOOL) canScroll
{
    if (General/self documentView] frame].size.height > [self documentVisibleRect].size.height)
        return YES;
    if ([[self documentView] frame].size.width > [self documentVisibleRect].size.width)
        return YES;
	
    return NO;
}



//  tile -- Override to update the document cursor.
/*- (void) tile
{
    //[super tile];
	
    //  If the user can scroll right now, make our document cursor reflect that.
	if ([self canScroll])
        [self setDocumentCursor: [[self class] dragCursor;
    else
        [self setDocumentCursor: General/[NSCursor arrowCursor]];
}*/



//  dragDocumentWithMouseDown: -- Given a mousedown event, which should be in
//  our document view, track the mouse to let the user drag the document.
- (BOOL) dragDocumentWithMouseDown: (General/NSEvent *) theEvent // RETURN: YES => user dragged (not clicked)
{
	General/NSPoint     initialLocation;
    General/NSRect      visibleRect;
    BOOL      keepGoing;
    BOOL      result = NO;
	
	initialLocation = [theEvent locationInWindow];
    visibleRect = General/self documentView] visibleRect];
    keepGoing = YES;
	
    while (keepGoing)
		{
        theEvent = [[self window] nextEventMatchingMask: [[NSLeftMouseUpMask | General/NSLeftMouseDraggedMask];
        switch ([theEvent type])
			{
            case General/NSLeftMouseDragged:
				{
					General/NSPoint  newLocation;
					General/NSRect  newVisibleRect;
					float  xDelta, yDelta;
					
					newLocation = [theEvent locationInWindow];
					xDelta = initialLocation.x - newLocation.x;
					yDelta = initialLocation.y - newLocation.y;
					
					//  This was an amusing bug: without checking for flipped,
					//  you could drag up, and the document would sometimes move down!
					if (General/self documentView] isFlipped])
						yDelta = -yDelta;
					
					//  If they drag MORE than one pixel, consider it a drag
					if ( (abs (xDelta) > 1) || (abs (yDelta) > 1) )
						result = YES;
					
					newVisibleRect = [[NSOffsetRect (visibleRect, xDelta, yDelta);
					General/self documentView] scrollRectToVisible: newVisibleRect];
				}
				break;
				
            case [[NSLeftMouseUp:
                keepGoing = NO;
                break;
				
            default:
                /* Ignore any other kind of event. */
                break;
			}                // end of switch (event type)
		}                  // end of mouse-tracking loop
	
    return result;
}
@end


----
I'm not sure how this is working.  You can not add instance variables to a category.  General/SynchroScrollView should be a subclass of General/NSScrollView instead to add an instance variable. 

----
It's not an instance variable, it's a global. This probably means that this category can only be used on one pair of views in any given app.

----
Ahh yes..  see that now.. added "global" to comment on that line so it isn't so easily overlooked by others.. :)

----
I wonder why in the Apple code that part of this is taken from, the synchronizedViewContentBoundsDidChange method uses General/self contentView] scrollToPoint:newOffset] instead of [[self documentView] scrollPoint:newOffset]?  Calling scrollPoint on the documentView is more general I think unless it doesn't work in all cases.  I needed to use the documentView method  for horizontally scrolling (with a change to get the x offset) [[NSTableViews with column headers.  Otherwise horizontally scrolling a General/NSTableView with contentView->scrollToPoint scrolls the columns only and not the column headers.  -DB
