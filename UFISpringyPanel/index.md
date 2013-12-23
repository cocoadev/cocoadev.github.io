---
layout: page
title: UFISpringyPanel
---



see also General/WhereIsCoolSystemPrefsWindowEffect

When you lay out windows in Cocoa, you can use autosizing springs to make your window content change size when you resize the windows, using the built in General/AppKit "Box & Spring" approach. What can you do when you want the opposite effect? ie, you want a window to change size when something inside it changes its size...

Here's a solution I came up with this week, after spending a few evenings persuing blind alleys. I'm not claiming it will work in all circumstances, but it seems pretty good for my purposes.

Lets start by explaining the problem with some code:

     

@interface General/LineBoxField : General/NSTextField

@implementation General/LineBoxField
- (void) setFont: (General/NSFont *) a_font {
    [super setFont: a_font];
    [self sizeToFit];
}

- (void) sizeToFit {
    float oldHeight = General/NSHeight([self bounds]);
    [self setPostsFrameChangedNotifications: NO];
    [super sizeToFit];
    float heightDiff = oldHeight - General/NSHeight([self bounds]);
    General/NSPoint origin = [self frame].origin;
    [self setFrameOrigin: General/NSMakePoint(origin.x, origin.y + heightDiff)];
    General/self superview] setNeedsDisplay: YES]; //needed to ensure we don't get garbage when the field shrinks

    //this actually causes a notification to be fired
    [self setPostsFrameChangedNotifications: YES];

}
@end



This is code you could paste into pretty much any control to make it resize when its font is changed. As you can see it overrides the standard [[NSControl's **sizeToFit:** method to resize the control and move it down by the amount the height changed. (As I'm sure you know Cocoa uses a bottom-left co-ordinate system, so if you want to top left corner of a view in the same place and resize it down and left, one has to adjust the origin like this - you'll see this theme repeated in other code in this article). I'm managing setPostsFrameChangedNotifications: (which is ON by default) to ensure the code only generates a single frame     General/NSViewFrameDidChangeNotification If you don't do this, you get a Notification for     [super sizeToFit] and for     [self setFrameOrigin: General/NSMakePoint(origin.x, origin.y + heightDiff)]

So far we have an General/NSControl with nice resize behaviour triggered when its font is changed, now we'll look at the source code for a panel that will listen for     General/NSViewFrameDidChangeNotification and resize itself appropriately:


     
#import <Cocoa/Cocoa.h>

@interface General/UFISpringyPanel : General/NSPanel {
@private
}

@end

#import "General/UFISpringyPanel.h"
#import "General/UFIViewAdditions.h"
#import "General/UFIWindowAdditions.h"

@implementation General/UFISpringyPanel

- (void) awakeFromNib {
    [self contentResized: General/[NSNotification notificationWithName:
        General/NSViewFrameDidChangeNotification object: [self contentView]]];
}

- (void) setContentView: (General/NSView *) a_view {
    [super setContentView: a_view];
    [self observeSizeChanges: @selector(contentResized:)];
}

- (void) dealloc {
    General/[[NSNotificationCenter defaultCenter] removeObserver: self];
    [super dealloc];
}

@end

 

That was fairly painless! Of course, all of the real code is hidden in the pair of Categories     General/UFIViewAdditions and     General/UFIWindowAdditions. I deliberately structured the code this way so if I ever need to create a     General/UFISpringyWindow to complement this     General/UFISpringyPanel I will only need to copy and paste the few lines above and reuse the Categories.

First, the view addition:

     
#import <Cocoa/Cocoa.h>

@interface General/NSView (General/UFIViewAdditions)
- (void) registerObserver: (id) a_observer forSizeChanges: (SEL) a_callback;
- (General/NSSize) checkIntersections: (General/NSRect) a_frame;
@end

#import "General/UFIViewAdditions.h"
#define max(a,b) (((a)>(b))?(a):(b))

@implementation General/NSView (General/UFIViewAdditions)

- (void) registerObserver: (id) a_observer forSizeChanges: (SEL) a_callback {
    General/NSNotificationCenter *nc = General/[NSNotificationCenter defaultCenter];
    if ( [self postsFrameChangedNotifications] ) {
        [nc addObserver: a_observer
               selector: a_callback
                   name: General/NSViewFrameDidChangeNotification
                 object: self];
    }        
    General/NSArray *children = [self subviews];
    if ( children != nil) {
        int childCount = [children count];
        int i=0;
        for (i=0; i < childCount; i++) {
            General/NSView *child = (General/NSView *) [children objectAtIndex: i];
            [child registerObserver: a_observer forSizeChanges: a_callback];
        }
    }
}

- (General/NSSize) checkIntersections: (General/NSRect) a_frame {
    General/NSRect intersection = General/NSIntersectionRect([self frame], a_frame);
    General/NSSize delta = General/NSMakeSize(General/NSWidth([self frame]) - General/NSWidth(intersection),
                                  General/NSHeight([self frame]) - General/NSHeight(intersection));
    General/NSArray *children = [self subviews];
    if ( children != nil) {
        int childCount = [children count];
        int i=0;
        for (i=0; i < childCount; i++) {
            General/NSSize childDelta = General/children objectAtIndex: i] checkIntersections: a_frame];
            delta = [[NSMakeSize(max(delta.width, childDelta.width), max(delta.height, childDelta.height));            
        }
    }
    return delta;
}

@end



These two methods walk the     General/NSView's subview heirarchy recursively. The first allows an object to register itself as an observer for     General/NSViewFrameDidChangeNotifications coming from the view it applies to or any of its descendants. The second method looks for child views that are clipped by a rectangle     a_frame.  It walks recursively through the children building up an     General/NSSize delta that records the largest width and height that falls outside     a_frame.

Now the window addition:

    
#import <Cocoa/Cocoa.h>

@interface General/NSWindow (General/UFIWindowAdditions)
- (void) observeSizeChanges: (SEL) a_callback;
- (void) setFrameSizeMaintaingOrigin: (General/NSSize) a_newFrameSize;
- (void) contentResized: (General/NSNotification *) a_notification;
@end

#import "General/UFIWindowAdditions.h"
#import "General/UFIViewAdditions.h"

@interface General/NSWindow (General/UFIWindowAdditionsPrivateMethods)
- (void) sizeToFit;
@end

@implementation General/NSWindow (General/UFIWindowAdditions)

- (void) observeSizeChanges: (SEL) a_callback {
    General/NSView *view = [self contentView];    
    if ( view == nil ) {
        General/[[NSNotificationCenter defaultCenter] removeObserver: self];
    } else {
        [view registerObserver: self forSizeChanges: a_callback];
    }
}

- (void) contentResized: (General/NSNotification *) a_notification {
    if ( [self contentView] != nil) {
        General/[[NSNotificationCenter defaultCenter] removeObserver: self];
        [self sizeToFit];
        [self observeSizeChanges: @selector(contentResized:)];
    }
}

- (void) sizeToFit {
    General/NSSize size = [self minSize];
    General/NSSize maxSize = [self maxSize];
    General/NSSize delta = General/NSMakeSize(-1, -1);
    while ( size.width <= maxSize.width && size.height <= maxSize.height && !General/NSEqualSizes(delta, General/NSZeroSize) ) {
        [self setFrameSizeMaintaingOrigin: size];
        delta = General/self contentView] checkIntersections: [[self contentView] bounds;
        size.width += delta.width;
        size.height += delta.height;
    }
}

- (void) setFrameSizeMaintaingOrigin: (General/NSSize) a_newFrameSize {
    General/NSPoint origin = [self frame].origin;
    float originalHeight = General/NSHeight([self frame]);
    origin.y += (originalHeight - a_newFrameSize.height);
    [self setFrame: General/NSMakeRect(origin.x, origin.y, a_newFrameSize.width, a_newFrameSize.height) display: YES animate: YES];
}

@end


The window addition is the core of the code. 

    observeSizeChanges: � tells the window to begin listening for changes to the sizes of its     contentView's children


    setFrameSizeMaintaingOrigin: � is a helper method that allows one to resize a window without its top left corner moving around

    contentResized: � a public method that informs the window that its content has been resized and that it should recalculate it's size. It will usually be called when     General/NSViewFrameDidChangeNotification is fired by one of the window's children, but other objects can explicitly call the method to force a recalculation of the size when they know one will be needed.

    sizeToFit � is the private method that performs the actual work of resizing the window.

Firstly, this method is private for a particular reason. Note how     contentResized: calls     sizeToFit but it brackets the call with     General/[[NSNotificationCenter defaultCenter] removeObserver: self]; and     [self observeSizeChanges: @selector(contentResized:)];, ie, it disables listening for     General/NSViewFrameDidChangeNotification's from the window's children before calling     sizeToFit and then re-enables them after its done. This is necessary otherwise one will tend to get an infinite loop: calling     sizeToFit will cause a resize, which will cause an     General/NSViewFrameDidChangeNotification to be sent, which would call     contentResized:, which would call     sizeToFit again, making a loop. Temporarily disabling observing the     General/NSViewFrameDidChangeNotification's prevents the loop.

Here's the algorithm that causes the window to resize itself:


*set the window to its     minSize as defined in General/InterfaceBuilder or via code
*loop while the window is less than its     maxSize
    
    *use     checkIntersections to see if all of the children fit within the current     bounds of the window
    *if all children fit, stop
    *otherwise make the window larger by the amount indicated by     checkIntersections



This algorithm allows the window to grow if it is too small for its current contents. If the window is too large for its contents, the algorithm works by first setting it to its mimimum size at the begining. It then grows, finding the smallest possible size that can work: this allowing the window to shrink if its contents has shrunk. The size is re-evaluated every time a child view changes size, as we are listening for     General/NSViewFrameDidChangeNotification from all of the children.

For this to work correctly and look good you have to have a window which reflows nicely when it is resized. You can achieve this in the usual way by using     General/NSBoxes and setting up the autosizing springs in General/InterfaceBuilder. Make your window resizable and use IB's test mode to ensure your window reflows nicely when you drag it by its corner. If you do not want users to be able to resize the window by hand when your application is finished, you can disable manual resizing by the grow box once you are happy with the behaviour.

Aside: You might be wondering why we actually have to resize the window to its minimum size and grow it outwards again. Wouldn't it be simpler to just pass     self minSize into     checkIntersections, and go around the loop until the correct size has been determined, then just call     setFrame: on the window with the final calculated size at the end? You'd think this would work, but it turns out to fail in many cases because of the autosizing springs. Consider a case where your window is too large: one of its children just became smaller and you want the window to shrink to the smallest size it can be given the new smaller contents. The trouble comes from the way one usually nests boxes in Cocoa. Its true that one child has just shrunk, but what it its previous larger size had caused some other springy component in the window to also be large? Well, then the window would never shrink because the large sibling would keep the window large, even though the view that triggered the resize has shrunk. Therefore it is necessary to actually shrink the window so all children with their autosizing springs set are forced to be as small as they can be. (Don't worry if you can't follow this paragraph: its not important to understand this to actually use the code and its difficult to put into words.)

Well, that's the algorithm. It works for me in the case I need it too. As my text field's font changes, the window containing the field resizes itself, growing and shrinking to fit.

Comments please!

----

That's cool but applications that do that can be annoying when you have other windows that you don't want covered (for an example see Apple's iCal - annoys the hell out of me - 3 different sizes depending on day|week|month view? Growl!). It goes against style guidelines. Users should have the power to fix the size and location of the windows. If you have behaviour like this, perhaps consider making it optional using preferences. --   General/MikeAmy

----

If you notice with iCal, you can set the size of the 3 different views and they'll each remember their respective sizes. If you want them all to be the same, set them the same. A possible preference for iCal would be "Retain separate window sizes per calendar view" or the like.  --Arbini
