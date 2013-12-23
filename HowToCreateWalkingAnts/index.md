---
layout: page
title: HowToCreateWalkingAnts
---

Does anyone know how to make the rectangular selection box (commonly known as "the walking ants")?  Which Cocoa classes and methods might be helpful?  I have a custom view in which I would like the user to be able to click and drag a rectangular selection box.  -- AlexanderD

----

I think it's worth pointing out that Mac OS X seems to be moving away from marching ants. Finder, Sketch, iPhoto all use a simple rectangle to indicate selection. I would probably stick with that kind of approach myself.

I'm not sure what the question is here, so I'll touch on a bunch of topics that will hopefully become the starting point of a more complete solution.

1) In order to have a selection box, you must have something to select. That is, something that contains a bunch of objects. This container is most likely an NSView. The objects you are selecting may themselves be NSView<nowiki/>s, or they could be composited by NSCell<nowiki/>s by the containing view. For simplicity, let's assume we have only one view; that view keeps track of the selectable items and draws them using NSCell<nowiki/>s.

2) You will need to track the click and drag event. This is commonly done in mouseDown: like so

    
// borrowed from the source to Sketch
NSRect SKTRectFromPoints(NSPoint point1, NSPoint point2) {
    return NSMakeRect(((point1.x <= point2.x) ? point1.x : point2.x), 
    ((point1.y <= point2.y) ? point1.y : point2.y), 
    ((point1.x <= point2.x) ? point2.x - point1.x : point1.x - point2.x), 
    ((point1.y <= point2.y) ? point2.y - point1.y : point1.y - point2.y));
}

- (void)mouseDown:(NSEvent*)theEvent
{
    NSPoint origPoint, curPoint;

    origPoint = curPoint = [self convertPoint:[theEvent locationInWindow] fromView:nil];

    for (;;) {
        theEvent = self window] nextEventMatchingMask:([[NSLeftMouseDraggedMask | NSLeftMouseUpMask)];
        curPoint = [self convertPoint:[theEvent locationInWindow] fromView:nil];

        // mSelectionRect is an ivar
        mSelectionRect = SKTRectFromPoints(origPoint, curPoint);
        [self setNeedsDisplayInRect:mSelectionRect];

        // if you want marching ants, maybe set up some kind of animation timer
        // if (NSEqualRects(NSZeroRect, mSelectionRect)) {
        //     [self stopAntAnimationTimer];
        // } else {
        //     [self startAntAnimationTimer];
        // }

        if ([theEvent type] == NSLeftMouseUp) {
            break;
        }
    }
}


3) You will need to draw the selection. Add that to your drawRect method.

    
- (void)drawRect
{
    // draw all of our items. mItemArray is an NSArray of Item classes. We also
    // define an ItemCell class, which is an NSCell subclass for drawing our items.
    // There are many, many ways to do this, but this should illustrate the basic point.
    Item *item;
    NSEnumerator *e;

    e = [mItemArray objectEnumerator];
    while (item = [e nextObject]) {
        NSRect itemFrame = [item frame];
        BOOL selected = NSIntersectsRect(mSelectionRect, itemFrame);

        [item setSelected:selected];
        [mItemCell setItem:item];
        [mItemCell drawWithFrame:itemFrame inView:self];
    }

    // draw our selection rect
    if (!NSEqualRects(NSZeroRect, mSelectionRect)) {
        [[NSColor blackColor] set];
        NSFrameRect(mSelectionRect);
        // or if you are marching, instead apply some kind of pattern. the following
        // is untested code, but might get you started. mPhase should be set in your
        // animation timer handler, and probably should be within the range [0, 7),
        // i.e., the set {0, 1, 2, 3, 4, 5, 6}.
        // NSBezierPath *selectionPath = [NSBezierPath bezierPathWithRect:mSelectionRect];
        // float dashArray[2] = {5.0, 2.0};
        // [selectionPath setLineDash:dashArray count:sizeof(dashArray) / sizeof(dashArray[0]) phase:mPhase];
        // [selectionPath stroke];
     }
}


4) Again, if you want the selection to march, you'll need animation timers:

    
- (void)startAntAnimationTimer
{
    mAntTimer = [[NSTimer scheduledTimerWithTimeInterval:0.5 
                    target:self 
                    selector:@selector(handleAntAnimationTimer:) 
                    userInfo:nil 
                    repeats:YES] retain];
}

- (void)stopAntAnimationTimer
{
    [mAntTimer invalidateTimer];
    [mAntTimer release];
    mAntTimer = nil;
}

- (void)handleAntAnimationTimer:(NSTimer*)timer
{
    mPhase = (mPhase + 1) % 7;
    [self setNeedsDisplayInRect:mSelectionRect];
}


And that's about it. You should be able to fill in the blanks yourself. Let us know how the animation thing works out, although again I suggest avoiding it altogether. Really, it looks cheesy when compared with the rest of the Aqua interface. See SketchApp for a concrete example of modern rubberbanding.

-- MikeTrent
----

Mike,

   Thank you so much, all this is very helpful.  I guess you are right about the marching ants being retro, a good semi-transparent grey line should be sufficient.  Anyway, it will take me a while to digest all this information. --AlexanderD

----

See also: SketchApp RubberBanding
----
Isn't there a method in cocoa to do the selection? I have done a simple app where you can zoom in on a part of the picture by selecting, and I had to do the selection-rectangle myself, like the above example. Is there not a way to access the selection method of iPhoto, or the method of Screen Captures? -- EnglaBenny (sorry for being anon!)

Dear anonymous. "Isn't there a method in cocoa to do the selection?" No. I discuss this in point #1 above. There are many, many ways to design an application and Cocoa can't anticipate all of them. But a selection is basically a rectangle (point #3) and a tracking loop (point #2), so I don't think this is really a big deal. Heck, Cocoa even helps you with dashed-line offsets (point #4) ... I'm not sure how this could be this any easier and still be useful for a wide variety of ProgrammingPatterns. "Is there not a way to access the selection method of iPhoto, or the method of Screen Captures?" Again, the idea of selection is application specific. That is, the very notion that you have something to select is specific to an application, so selection is also specific to that application. Therefore iPhoto and others all have their own selection logic embedded within themselves; there's no (practical) way to "access" this.-- MikeTrent

Perhaps anonymous was thinking of NSHighlightRect -- AngelaBrett

I don't know, therefore I ask -- EnglaBenny/anonymous

Anonymous isn't a problem. I just wanted to make the break between question and answer clear w/o using a line. -- MikeTrent
----
I changed     [mAntTimer invalidateTimer];  to     [mAntTimer invalidate];  (typo i presume)
and added one line to read
    
		[self setNeedsDisplayInRect: NSIntegralRect(mSelectionRect) ];	// erase previous
		mSelectionRect = getRectFromPoints(origPoint, curPoint);
		[self setNeedsDisplayInRect:mSelectionRect];

to eliminate strange shadowing of the marquee.  PaulCD.
----
Thanks all, this was tremendously helpful.
For a while, I had marching half-ants, the inner halves moved, the outer halves didn't. The solution was to make a new rectangle, bigger than mSelectionRect by half of the path width on every side, and redisplay with handleAntAnimationTimer using this new rectangle. Robin Forder.

