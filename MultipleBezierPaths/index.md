---
layout: page
title: MultipleBezierPaths
---

I don't understand why Objective-C cannot permit for() loops in the drawRect: method of a NSView...

I can't do what I want to do without a for() loop. Here's a description of what I have done yet :

I have a NSMutableArray with some NSNumber objects in it.
These objects determine the width of rectangles that are to be drawn in my NSView.
I want to implement a loop, in which NSBezierPath objects are drawn depending on the value of the NSNumber objects in my array...

I tried to implement a for() in the drawRect method, but the loop never executes...

Last thing that may be important. My NSView is actually a subview of a NSScrollView...

-- Trax

----

I think you could just create a class method or something that would be able to do it. Search for: "regular polygon". You'll find some code in the topic that uses a for () loop and then returns a bezier path to the drawRect: method. 
I hope this is what your looking for. 

-- JohnDevor

----

Curious: why are you subclassing NSScrollView? You don't need to subclass that to get scrolling behaviour, only to override the scrolling behaviour NSScrollView provides (e.g. the scroll bars, et cetera). -- RobRix

----

My NSView is a subview of NSScrollView, but I didn't programmatically set the NSView to be the documentView of my NSScrollView... 

Could someone explain why a for loop will not iterate inside a method implementation of drawRect:. I do this all the time and I'm puzzled that this is even an issue. I think the problem with subclassing NSScrollView is that an NSScrollView doesn't draw anything (only subviews of NSScrollView draw things - NSScrollers and the document view). If you try to draw thing in the drawRect method of an NSScrollView I think the clip view (NSScrollView's contentView) will hide any drawing that is going on. You should really be subclassing NSView to do any drawing and setting the documentView of the scroll view to your custom NSView (MyView). 

    
    id scrollView=[[NSScrollView alloc] initWithFrame:scrollViewframe];
    id myView=[[MyView alloc] initWithFrame:myViewFrame];
    [scrollView setDocumentView:myView];


----

The trick for regular polygons is good, but for what I want to do, I don't think it could be efficient, since I imperatively need more than one NSBezierPath, because I want my NSView to draw multiple rectangles...

I found something else strange. With multiple NSLogs scattered around my code, I found that whenever I try to get [myArray count], it returns zero, even though there ARE objects in it... That's why my for() doesn't do anything.

Here's a part of my code...
    
- (void)drawRect:(NSRect)rect
{
    [[NSGraphicsContext currentContext] setShouldAntialias:NO];
    [NSBezierPath setDefaultLineWidth:1.0];
    
    NSRect lim = [self bounds];
    int h = lim.size.height-8;
    int i, acc = 0;
    NSLog(@"Count is : %i",[lis count]); // <-- Returns zero !!!
    
    for (i=0; i<[lis count]; i++) {
    NSLog(@"Count is : %i",i);
        [[NSBezierPath bezierPathWithRect:NSMakeRect(acc,5,[[lis objectAtIndex:i] intValue],h)] stroke];
        acc += [[lis objectAtIndex:i] intValue] + 4;
    }
}



The problem had to do with an incorrectly linked outlet and several incorrectly linked actions. Minor mistakes that are easily overlooked.

