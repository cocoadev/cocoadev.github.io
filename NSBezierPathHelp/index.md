---
layout: page
title: NSBezierPathHelp
---



I implemented this code into a very simple application, but still can't get any results. "vue" is my NSView where I want the path to be drawn. That piece of code doesn't deliver any result. Can anyone give me a clue ?

    
- (void)drawRect:(NSRect)rect
{
    rect = [vue bounds]; 
    
    NSBezierPath *chemin;
    NSPoint p1 = NSMakePoint([lignex1 intValue], [ligney1 intValue]);
    NSPoint p2 = NSMakePoint([lignex2 intValue], [ligney2 intValue]);
    
    [chemin moveToPoint:p1];
    [chemin lineToPoint:p2];
    
    NSColor *couleur = [NSColor
                colorWithCalibratedRed:([rouge intValue]/255.0)
                green:([vert intValue]/255.0)
                blue:([bleu intValue]/255.0)
                alpha:([alpha intValue]/255.0)];
    
    [couleur set];
    [chemin stroke];
    
}


I also want to make the NSView draw itself ONLY when the user presses a button.

----

    
- (void)drawRect:(NSRect)rect
{
    rect = [self bounds]; // <-- You are implementing this in a sublass of NSView, right?
    
    NSBezierPath *chemin = [NSBezierPath bezierPath]; // Instantiate object "chemin" (autoreleased)



For the button thingie, see -setNeedsDisplay: in NSView.

-- PerSquare

----

Okay, I think I was not precise enough. I want a button to draw a line, another one to draw a rectangle, and another one to draw a circle. How do I implement the drawRect: method to make a different drawing for each button ?

----
Hmm, you really don't do that in drawRect: in general. I suggest you:
  
*  Subclass NSView (necessary)
*  Add an instance variable NSBezierPath *shape; to it
*  Add a method setShape:(NSBezierPath ')theShape;
*  Do a [myView setShape:someShape] in your different button actions and
*  issue [myView setNeedsDisplay:YES] after a shape is changed
  

Read up om ModelViewController and check out the examples (on your HD)

* /Developer/Examples/AppKit/DotView
* /Developer/Examples/AppKit/BezierPathLab
  
in that order.

Good luck,
PerSquare

