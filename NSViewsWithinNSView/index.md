---
layout: page
title: NSViewsWithinNSView
---

I have a custom NSView, and I want it to contain several instances of another custom NSView, making them subviews of the main NSView. Each of the subviews have its own tracking rect so it can react to mouse movements and be dragged around, but whenever I add more than one of the subviews to the main NSView only one of them responds to mouse events. I am fairly sure that this is because when I instantiate one of my subviews I do it from the main NSView in the following manner :

    
CustomSubView *subViewInstance = [[CustomSubView alloc] initWithFrame:[self bounds]];
[self addSubview: subViewInstance];


I think it is the fact that I am doing an initWithFrame and allocating the new subview the entire bounds of the main NSView to work with. I chose this way because it seemed convenient. But now I think doing it this way is precluding mouse events getting to other subviews that are "underneath" this subview that has claimed the entire canvas. 

I need some advice as to whether this is happening, and as to whether being much more specific about the frame I give the subclasses would alleviate this problem.

It seems a bit more of a pain to calculate the frame of the subview before I create it but I suppose I could live with that. How does the whole bounds thing work if I want my subview to grow and shrink over time? Do I claim the maximum area necessary when I create it, and then just draw into it as necessary (this may create the overlapping problem above again) or do I grow and shrink the frame/bounds as I require? As you can probably tell, I am a little confused about custom subviews! 

----

I recently wrote a "layout manager" for handling large numbers of subviews. In my experience, there's no mouse/keyboard event gobbling, but then I'm setting the view's bounds when it's inserted and whenever the container view is resized. I posted the code in SampleCode under FlowLayoutView; it's not the best code in the world, but it works for me, and it's pretty efficient. Which is to say, I embedded something like 200 subviews + a scrollview to pan around, and still had perfectly decent frame resize performance.

----

Thanks, but I suppose there's one thing that would really help me out... When I call initWithFrame: on a newly allocated subview from my "container" view, I pass it an NSRect. But what are those NSRect details for? Is it (A) The origin and size of the new subview relative_to_the_container_view, or (B) is it the origin and size of the frame independent of the view it is placed in?

----

The origin is relative to the container view.

