---
layout: page
title: QuartzCompositionsNotShowingOutputKey
---



Here's the thing...

I'm loading a Quartz Composition in q QCView in Cocoa and when I start the rendering the first time, I can get the list of Output Keys, but not the value of a specific key. Here is the code:

[circleGraphic loadCompositionFromFile:pointerPath];
NSArray *allOutputKeys = [NSArray arrayWithArray:[circleGraphic outputKeys]];
[circleGraphic startRendering];
		
NSLog(@"All output keys %@",[circleGraphic outputKeys]);
NSLog(@"Output key value (should be a string): '%@'",[circleGraphic valueForOutputKey:@"customName1"]);
		
Here is the result in console:


2006-10-25 20:18:02.841 TestApp[1217] Output keys (
    customName1,
)
2006-10-25 20:18:02.841 TestApp[1217] Output key value  (should be a string): *

So the value contained in this key seemsempty (but it is not!

If I call this method twice, the second time I get:

2006-10-25 20:18:02.841 TestApp[1217] Output keys (
    customName1,
)
2006-10-25 20:18:02.841 TestApp[1217] Output key value  (should be a string): 'Excellent Value'


Where "Excellent Value" is the proper value stored in the Composition's output key.

It seems like as long as the composition is not rendering the values appear empty, and just starting the rendering before may not leave enough time... not too sure.

What would be the right way to do this? It looks like there are notifications, should I use those?

