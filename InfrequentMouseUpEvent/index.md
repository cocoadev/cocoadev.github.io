---
layout: page
title: InfrequentMouseUpEvent
---

Hey, I have my app set up to get MouseDown and MouseUp events in a NSView. 

- (void)mouseUp:(NSEvent *)theEvent; {
		NSLog(@"Up");
}

- (void)mouseDown:(NSEvent *)theEvent; {
		NSLog(@"Down");
}


Very simple code. What I have noticed however is that if I click the mouse with a regular click, or if I double click fast enough, then I get only a mouseDown event.
There seems to be a small delay in how long I need to hold down my mouse button before I get the up event. Is this just something I have done crazily in my app somewhere
or is there a delay setting I have missed? The delay seems to be very very small, but I have noticed it on a few occasions now.

Thanks for any help/insights.


EDIT: I think I figured out that it has to do with a Mouse Click vs a Mouse Down. If I click fast enough then Mac OS X interprets it as a Mouse click. I found a few resources on this, but unfortunately none tell me how to always force a mouse down event :(

