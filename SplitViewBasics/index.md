---
layout: page
title: SplitViewBasics
---



While it may seem obvious to more experienced Cocoa programmers, creating a NSSplitView in InterfaceBuilder is not as obvious to people new to the tools. Luckily it is rather easy:

First create the Views that you would like to have in your NSSplitView by dragging them from the palette. Then select them all at once and select Make subview of -> Split View from the Layout menu. To do some more interesting things you will have to set a delegate for the NSSplitView and do things programmatically as it does not appear that you can do any adjustments in Interface Builder.

----

Here's how you can control an NSSplitView with a delegate:


    *You start by setting the split view's delegate
    *You need to implement splitView:resizeSubviewsWithOldSize:
        
            *This method (splitView:resizeSubviewsWithOldSize:) is the trickiest method to implement because many factors affect the tiling (subview arrangement) of the subviews. * see the example below for more information on how to implement this method *  
        
    *To control the minimum dimension of a subview implement splitView:constrainMinCoordinate:ofSubviewAt: (see AppKit documentation)
    *To control the maximum dimension of a subview implement splitView:constrainMaxCoordinate:ofSubviewAt: (see AppKit documentation)
    *To control the position of the divider implement splitView:constrainSplitPosition:ofSubviewAt: (see AppKit documentation)


There are a couple of other delegate methods, but the ones listed above are the main ones. 

Constraining the minimum coordinate influences two split view behaviors. The first is to constrain the minimum allowable size of each split view subview. The other effect this constraint has is to define how a subview collapses. If a divider is moved past a minimum coordinate constraint the split view stops adjusting the subviews until a halfway point is reached. This halfway point is half the width or height of the minimum coordinate. Let's say you have set the minimum coordinate to 100, and your split view is a vertical split view (the divider runs vertically). The widths of each subview cannot be resized less than 100. The divider will be pinned at a position to accommodate this minimum coordinate value. If the user continues to attempt to shrink the subview to a width that is less than 100, by moving the cursor away from the divider and into the subview, the subview will collapse completely once the cursor has moved past the halfway point (1/2 the width of the subview).

All of this is really simple if you are only using one divider. The value that you return in **splitView:constrainMinCoordinate:ofSubviewAt:** and **splitView:constrainMaxCoordinate:ofSubviewAt:** is the value for the subview to the left or above the divider being dragged (the divider being dragged is designated by the (int)offset argument, the first divider <left to right or top to bottom> is 0, therefore if you only have two subviews in a split view the offset value will always be 0). If the divider is a vertical divider then the value you are returning is the minimum/maximum width, and if the divider is a horizontal divider then the value you are returning is the minimum/maximum height. The value you return in **splitView: constrainSplitPosition:ofSubviewAt:** is the value you would like to keep the divider away from the boundary to the right of or beneath said divider (is that proper grammer?). Anyways, what I'm trying to say is that you can define the minimum size of the subview to the right of or below the divider by implementing **splitView: constrainSplitPosition:ofSubviewAt:**. For example if you would like to prevent the subview to the right of the divider from being more narrow than 100, all you have to do is return 100 in **splitView: constrainSplitPosition:ofSubviewAt:**. So to recap, the methods to control the dimensions of the subview to the left of or above the divider are **splitView:constrainMinCoordinate:ofSubviewAt:** and **splitView:constrainMaxCoordinate:ofSubviewAt:**, and the method to control the dimensions of the subview to the right of or below the divider is **splitView: constrainSplitPosition:ofSubviewAt:**. 

Things get really dicey once you introduce multiple dividers (2+ subviews). You basically treat everything to the left of or above the divider as the "leading entity" and everything to the right of or below the divider as the "trailing entity".

----

Now to the really confusing method. **splitView:resizeSubviewsWithOldSize:**

This method is not documented very well, and it took me a whole bunch of runtime logging to actually figure out what is going on here. 

**
First off, you have to be aware of something that IB does behind the scenes that can make your life miserable if you don't catch it up front. 
**
IB will connect the document view of NSTextViews to the IBOutlet, so if you are attempting to customize the tiling behavior of an NSSplitView you have to be aware of this. Tiling involves adjusting subviews within a split view so that all the subviews fill the split view (including dividers). If you assume that an outlet is the scrollview of a text view, you will be tiling the subviews of your split view based on frame dimensions for the document view not the scroll view!! Introspection is the best way to avoid this. The first thing you should do when prototyping custom split view tiling behavior is to log the class descriptions of each subview in your split view.

    
    NSLog(@"leftTextView: %@ rightTableView: %@",
        NSStringFromClass([leftTextView class]), NSStringFromClass([rightTableView class]));


If leftTextView tells you that it is a text view than, you have to check to see if its superview is a clip view. If the leftTextView's superview is in fact a clip view than the subview you want to tile in the split view is the scroll view for the text view. The scroll view should be the superview of the superview of the text view. It can't hurt to check to see if the superview of the scroll view is in fact the split view. Use the same introspection techniques to check to see that you have the correct subview for the right subview. I'm not sure what IB connects to when dealing with NSTableView's, so you have to check yourself if you plan on customizing the tiling behavior of a split view that has NSTableViews added and connected in IB. It probabaly isn't a bad idea to do this for every connection you make in IB to subviews of a split view.

After you are confident that you dealing with the right subviews in your split view you can start customizing the tiling behavior. 

*One solution is to bypass all of this in IB. Lay out two (or whatever number) NSView custom views, select them both, then do "Make Subviews Of ... Split View". Then place all your interface elements *inside* the custom views. It does take a lot of double-clicking to drill down and get to individual controls and IB seems to bog down if nesting gets too complicated, but it can save hours of frustration down the line ... ;-)*

**Second important piece of info, **

**splitView:constrainMinCoordinate:ofSubviewAt:, splitView:constrainMaxCoordinate:ofSubviewAt:** and **splitView:constrainSplitPosition:ofSubviewAt:** do not control the resizing behavior of a split view when the window is resized, so you can toss out all the rules defined by these delegate methods when tiling a split view with **splitView:resizeSubviewsWithOldSize:**. The "constrain" methods only assist the split view when a divider is being dragged. The "resize" method tiles/adjusts the subviews when the window is resized or when the splitview is resize by a superview. 

    
- (void)splitView:(NSSplitView *)sender resizeSubviewsWithOldSize:(NSSize)oldSize {
    id leftClass = NSStringFromClass([leftTextView class]);
    id rightClass = NSStringFromClass([rightTableView class]);
    NSLog(@"leftTextView: %@ rightTableView: %@", leftClass, rightClass);
    float dividerThickness = [sender dividerThickness];
    id leftTextViewScrollView = leftTextView superview] superview];
    
    // Here's how you check to make sure you are dealing with the correct subviews.
    // Both descriptions should confirm that the superviews are the same [[NSSplitView
    id leftDiscription = leftTextViewScrollView superview] description];
    id rightDiscription = [[rightTableView superview] description];
    [[NSLog(@"leftTextViewScrollView's superview: %@ rightTableView's superview: %@",
        leftDiscription, rightDiscription);
    NSRect newFrame = [sender frame];
    // The text view is in a scroll view so the frame 
    // you want to tile is the frame of the scroll view not the text view
    NSRect leftFrame = [ frame]; 
    // Not sure what IB connects to when dealing with table views so do whatever is
    // correct here. This is just an example and I have not tested this
    NSRect rightFrame = [rightTableView frame];
    // mySplitView has a vertical divider so the two subviews 
    // are tiled from left to right (text view to table view)
    if (sender==mySplitView) {
        // Here is how you customize the tiling behavior of a split view.
        // You start by setting the width of the left subview and
        // make the right subview fill in the rest of the split view
        // 
        // someValueOfYourChoosing is what ever you want it to be
        // withing the bounds of the splitview, you can implement the same
        // constraints here to agree with the constraints you placed on
        // dividers being dragged. You have to keep in mind that a window
        // doesn't care about the constraints placed here, so constrain
        // the window and the superview to mySplitView first before
        // thinking about how to constrain dimensions here. 
        leftFrame.size.width = someValueOfYourChoosing; 
        leftFrame.size.height = newFrame.size.height;
        leftFrame.origin = NSMakePoint(0,0);
        rightFrame.size.width = newFrame.size.width - leftFrame.size.width - dividerThickness;
        rightFrame.size.height = newFrame.size.height;
        rightFrame.origin.x = leftFrame.size.width + dividerThickness;
        [leftTextViewScrollView setFrame:leftFrame];
        // remember not to blindly set the frame of a view you think
        // is the right subview. In this case you make sure that
        // rightTableView is the correct subview to resize by observing
        // what gets logged for the superview description
        [rightTableView setFrame:rightFrame];
    }
}
 

Split views that have a horizontal divider flip the y axis, so the origin of the top subview is (0,0) and the origin of the bottom subview is (0, topSubviewFrame.size.height+dividerThickness). This flip is done because the order of the subviews and dividers goes from top to bottom. 

The parameter "someValueOfYourChoosing" is how you define the resizing behavior. By using the newFrame and oldSize dimensions, you can calculate a delta to resize the left subview. Once you have calculated a new size for the left subview you can place constraints on this new size (e.g. if (leftFrame.size.width<100) leftFrame.size.width=100).   --zootbobbalu

----

I found this works for me. The left view is an NSOutlineView, the right is an NSTextView:
    
- (void)splitView:(NSSplitView *)sender resizeSubviewsWithOldSize:(NSSize)oldSize
{
    // how to resize a horizontal split view so that the left frame stays a constant size
    NSView *left = sender subviews] objectAtIndex:0];      // get the two sub views
    [[NSView *right = sender subviews] objectAtIndex:1];
    float dividerThickness = [sender dividerThickness];         // and the divider thickness
    [[NSRect newFrame = [sender frame];                           // get the new size of the whole splitView
    NSRect leftFrame = [left frame];                            // current size of the left subview
    NSRect rightFrame = [right frame];                          // ...and the right
    leftFrame.size.height = newFrame.size.height;               // resize the height of the left
    leftFrame.origin = NSMakePoint(0,0);                        // don't think this is needed
     // the rest of the width...
    rightFrame.size.width = newFrame.size.width - leftFrame.size.width - dividerThickness;
    rightFrame.size.height = newFrame.size.height;              // the whole height
    rightFrame.origin.x = leftFrame.size.width + dividerThickness;  // 
    [left setFrame:leftFrame];
    [right setFrame:rightFrame];
}


-mrwalker

Works great - I just edited where it said "splitView subviews" to read "sender subviews".  Thanks much.

----

I found that I needed to add this bit just before setting the left and right frames. In the case when the window is zoomed large and the splitview thumb is positioned far to the right of the window, when the window is de-zoomed, the splitview thumb is clipped by the right edge of the window and thereby unreachable. The code below attempts to deal with that case by causing the splitview thumb to remain visible, adjacent to the right hand edge of the window after de-zooming.

    
    // handle the case of de-zoomed window clipping the splitview thumb
    if (rightFrame.size.width < dividerThickness) {
        leftFrame.size.width = newFrame.size.width - dividerThickness;
        rightFrame.size.width = newFrame.size.width - leftFrame.size.width - dividerThickness;
    }
    // original code below
    [left setFrame:leftFrame];
    [right setFrame:rightFrame];


--rantler

----

I found myself needing an NSSplitView with three subviews, that would obey the minimum sizes I had set in "-splitView:constrainMinCoordinate:ofSubviewAt:".  I'm not too advanced when it comes to computer algorithms, so I just did it with brute force.  Here's what I came up with:

    
- (void)splitView:(NSSplitView *)sender resizeSubviewsWithOldSize:(NSSize)oldSize
{
	[sender adjustSubviews];
	
	//////////////// maintain splitview subview widths of at least 128, unless collapsed
	
	NSArray *viewArray = [sender subviews];
	id viewOne = [viewArray objectAtIndex:0];
	id viewTwo = [viewArray objectAtIndex:1];
	id viewThree = [viewArray objectAtIndex:2];
	Float32 viewOneWidth = [viewOne frame].size.width;
	Float32 viewTwoWidth = [viewTwo frame].size.width;
	Float32 viewThreeWidth = [viewThree frame].size.width;
	
        while(viewOneWidth < 128)
	{
		if((int)viewOneWidth % 2 == 1 && viewThreeWidth > 128) --viewThreeWidth;
		else if(viewTwoWidth > 128) --viewTwoWidth;
		else --viewThreeWidth;
		
		++viewOneWidth;
	}
	
	while(viewTwoWidth < 128)
	{
		if((int)viewTwoWidth % 2 == 1 && viewOneWidth > 128) --viewOneWidth;
		else if(viewThreeWidth > 128) --viewThreeWidth;
		else --viewOneWidth;
		
		++viewTwoWidth;
	}
	
	while(viewThreeWidth < 128)
	{
		if((int)viewThreeWidth % 2 == 1 && viewTwoWidth > 128) --viewTwoWidth;
		else if(viewOneWidth > 128) --viewOneWidth;
		else --viewTwoWidth;
		
		++viewThreeWidth;
	}
	
	if(viewOneWidth != [viewOne frame].size.width)
	{
		[viewOne setFrame:NSMakeRect([viewOne frame].origin.x,
		    [viewOne frame].origin.y,
		    viewOneWidth,
		    [viewOne frame].size.height)];
	}
	
	if(viewTwoWidth != [viewTwo frame].size.width)
	{
		[viewTwo setFrame:NSMakeRect([viewTwo frame].origin.x,
		    [viewTwo frame].origin.y,
		    viewTwoWidth,
		    [viewTwo frame].size.height)];
	}
	
	if(viewThreeWidth != [viewThree frame].size.width)
        {
		[viewThree setFrame:NSMakeRect([viewThree frame].origin.x,
                    [viewThree frame].origin.y,
		    viewThreeWidth,
		    [viewThree frame].size.height)];
	}
}


This code assumes throughout that there is a minimum width on all subviews of 128 pixels and no maximum width.  I should also note that the subviews don't always shrink or grow equally, but they don't with Apple's default implementation as well.  I'd be curious if anyone has any ideas how to keep the resize equally distributed (over the course of a drag, of course, since it can never be equal between all views on a single step when the view widths are constrained to integer pixel values).

No promises as to performance, but it resizes smoothly on my PowerBook G4 1.33 GHz.  Further optimizations from the community would be appreciated.  Hope someone finds this helpful.

-- DanielToddCurrie, 2004-X-18

----

*Returning the favor (or maybe just passing it along ...)* 

**Description:**

This is a "split view delegate" class for a window with a vertical split (side-by-side subviews). An instance of this class is defined as a delegate of the splitview. 
As presented here, the split view has a small (narrower) view on the left which may be collapsed and a wider view on the right (which may *not* be collapsed). The left view will maintain its size when the window is made wider/narrower except when the right view doesn't have enough room, in which case the left view is narrowed (until it reaches its minimum).

If you try this out for yourself, you'll have to adjust the static float values for *leftFrameMin* and *rightFrameMin* to match your window definition. There's some comments in the code about this.

**Something happening here / What it is ain't exactly clear ...**

One thing I learned doing this (which may be discussed above but I didn't notice it) is exactly *when* the delegate messages do and *do not* get sent. It wasn't quite what I was expecting when I first read through the docs for NSSplitView. (See the comments for each of the methods in the code, and leave the log statements in when you first start playing with it so you can see when each method is invoked.)


**Note**: Parts of the following class are built around code found above (by "mrwalker" and "rantler").

    
/* MainSVDelegate.h */

#import <Cocoa/Cocoa.h>

@interface MainSVDelegate : NSObject
{
}
@end

--------------

/* MainSVDelegate.m */

#import "MainSVDelegate.h"

// The Main SplitView is a horizontal split. 
// The left frame is collapsible. 
// The right frame isn't.

@implementation MainSVDelegate

static float leftFrameMax  = 253.0f; // maximum width (left)

static float leftFrameMin  = 143.0f; // minimum width (left)
static float rightFrameMin = 395.0f; // minimum width (right)
/*================================================================================
 * IMPORTANT: leftFrameMin + rightFrameMin + the splitview's dividerThickness
 *            MUST add up to the minimum allowable frame width (determined by
 *            how you've defined the minimum width for the window). If you get
 *            the math wrong, especially if they add up to too much, the following
 *            code won't produce the desired effects. 
 *================================================================================
*/

/*================================================================================
 * canCollapseSubview message is sent ONLY when the divider is clicked on or when
 * the left view has already (just) collapsed (making a return of NO useless).
 * The message is sent once for each subview when you click on the divider.
 *================================================================================
*/
- (BOOL)splitView:(NSSplitView *)sender canCollapseSubview:(NSView *)subview {

    NSView *right = sender subviews] objectAtIndex:1];

    if (right == subview) {

[[NSLog(@"canCollapseSubview (right): denied");

       return NO;
    }

NSLog(@"canCollapseSubview (left): allowed");

    return YES;
}

/*================================================================================
 * constrainMinCoordinate message is sent ONLY when the divider is clicked on.
 *================================================================================
*/
- (float)splitView:(NSSplitView *)sender 
         constrainMinCoordinate:(float)proposedMin
                    ofSubviewAt:(int)offset {

    float allowed = proposedMin + leftFrameMin;

NSLog(@"constrainMinCoordinate %d: proposed=%f, allowed=%f", offset, proposedMin, allowed);

    return allowed;
}

/*================================================================================
 * constrainMaxCoordinate message is sent ONLY when the divider is clicked on.
 *================================================================================
*/
- (float)splitView:(NSSplitView *)sender 
         constrainMaxCoordinate:(float)proposedMax // = width if right were collapsed
                    ofSubviewAt:(int)offset { 

    float allowed = proposedMax - rightFrameMin;
    allowed = (allowed > leftFrameMax) ? leftFrameMax : allowed;

NSLog(@"constrainMaxCoordinate %d: proposed=%f, allowed=%f", offset, proposedMax, allowed);

    return allowed;
}

/*================================================================================
 * resizeSubviewsWithOldSize message is sent when the entire splitview changes 
 * size (e.g., when you resize the window and this affects the splitview's size).
 *================================================================================
*/
- (void)splitView:(NSSplitView *)sender resizeSubviewsWithOldSize:(NSSize)oldSize {

    NSRect newFrame = [sender frame];                           // get the new size of the whole splitView
    if (newFrame.size.width == oldSize.width) {                 // if the width hasn't changed
       [sender adjustSubviews];                                 // tell sender to adjust subviews
       return;
    }

    NSView *left = sender subviews] objectAtIndex:0];         // get the left subview
    [[NSView *right = sender subviews] objectAtIndex:1];        // and the right subview

    float dividerThickness = [sender dividerThickness];         // and the divider thickness
    [[NSRect leftFrame = [left frame];                            // current size of the left subview
    NSRect rightFrame = [right frame];                          // ...and the right

NSLog(@"resizeSubviewsWithOldSize old width: %f, new width: %f", oldSize.width, newFrame.size.width);

    float allowed = newFrame.size.width - (rightFrameMin + dividerThickness);
    allowed = (allowed >= leftFrameMax)? leftFrameMax:allowed;  // keep allowed <= max
    if (leftFrame.size.width > allowed)                         // left frame is too wide?
       leftFrame.size.width = allowed;                          // set it to allowable width
    leftFrame.size.height = newFrame.size.height;              

    rightFrame.size.width = newFrame.size.width - (leftFrame.size.width + 
                                                   dividerThickness);
    rightFrame.size.height = newFrame.size.height;              
    rightFrame.origin.x = leftFrame.size.width + dividerThickness; 

    // handle the case of de-zoomed window clipping the splitview thumb
    if (rightFrame.size.width < dividerThickness) {
       leftFrame.size.width = newFrame.size.width - dividerThickness;
       rightFrame.size.width = newFrame.size.width - (leftFrame.size.width + 
                                                      dividerThickness);
    }

    [left setFrame:leftFrame];
    [right setFrame:rightFrame];
}

@end

------

Hopefully this code will save you some time trying to figure this splitview stuff out.

Personally, I think this degree of control over splitviews should be something doable completely from within IB (without having to write any code). I don't see why this wouldn't be possible given the right parameters built into the NSSplitView class. If you agree, I've added a suggestion to that effect to AppKitMostWanted (see the entry dated **2004.09.30** there) where you can "vote for it" by adding 1 to the **AYES** count.

--PaulPomeroy, **2004.10.23**

*Fixed a misstatement in the above code. The right and left frame **minimums** + the divider thickness (which is 6 by default) must add up to the total width of the **splitframe** when the window is at its minimum allowed width.* (oct. 25)

----

I wish I had time to go through the above code and fix it, but most of those comments are wrong.  The thing is, when they say "ofSubviewAt:" they basically mean "ofDividerAt:", which if you think about it is what they should be saying.  If you're only ever getting a 0, that's because you only have 2 subviews.  Additionally, **splitView:canCollapseSubview:** does work, and a return of NO is useful, but I don't have time right now to see what you did wrong.

It really isn't a good idea to just *assume* that you've found a bug if things don't work as you expect.

*Okay, I removed the "apparently this is only sent for a ofSubviewAt:offset value of 0" comments. Thanks for clearing that up. As for the canCollapseSubView strangeness, I only know that I was seeing my "canCollapseSubView denied" log statement show up right when the view collapsed. Here's my *unconfirmed assumption* on this: if you return YES to the message that is sent when you click on the divider it doesn't matter if you then return NO for the message that gets sent when the view is actually collapsing. The above code isn't subject to this problem as it's always returning NO to the canCollapseSubview message for the left frame. Like you, I don't have the time to look into this right now. Maybe someone else can confirm or deny? Someone else with the time might also add the necessary code to set the **leftFrameMin** or **rightFrameMin** values instead of having them hard-coded (minimum allowed window width minus left/right padding around splitview minus divider thickness will get you the smallest width possible for the splitview and from there you can figure out, given one frames minimum, what the other's is) . That would make the whole process of using the code a lot easier. -- PaulPomeroy **2004.10.27***
----

**Note:** if you have multiple NSSplitView's with the same delegate, you must call     adjustSubviews on the split views that you don't adjust! Here's the modified code from above:
    if (sender == mainSplitView) // your split view
{
	NSView *left = sender subviews] objectAtIndex:0];
	[[NSView *right = sender subviews] objectAtIndex:1];
	float dividerThickness = [sender dividerThickness];
	[[NSRect newFrame = [sender frame];
	NSRect leftFrame = [left frame];
	NSRect rightFrame = [right frame];
	leftFrame.size.height = newFrame.size.height;
	leftFrame.origin = NSMakePoint(0,0);
	rightFrame.size.width = newFrame.size.width - leftFrame.size.width - dividerThickness;
	rightFrame.size.height = newFrame.size.height;
	rightFrame.origin.x = leftFrame.size.width + dividerThickness;
	[left setFrame:leftFrame];
	[right setFrame:rightFrame];
	[(NSSplitView *)right adjustSubviews]; // not sure if this is necessary (My right side is another split view)
}
else
{
	[(NSSplitView *)sender adjustSubviews]; // this is necessary or you get weird drawing behavior!
}


----

Also see RBSplitView.

