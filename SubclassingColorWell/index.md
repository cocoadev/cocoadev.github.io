---
layout: page
title: SubclassingColorWell
---



Here's the code:

    
#import "General/MyColorWell.h"

@implementation General/MyColorWell

- (General/IBAction)mouseDown:(id)sender
{
	int t = General/sender cell] tag];
	
	[[NSLog(@"General/MyColorWell -> mouseDown:");
	General/NSLog(@"tag = %i", t);
	[super mouseDown: sender];
}

@end


This fails at runtime with the error *  *** -General/[NSEvent tag]: selector not recognized [self = 0x35de30]*
The code runs fine with the omission of     General/sender cell] tag] code.
I'm trying to capture the "tag" field of one of three different colorWells in my interface so that I might handle the chosen colors diffenetly.

Thanks.

----

The parameter to a     mouseDown: message is not "sender", it's the [[NSEvent that generated the mouse down.
----
Ah... right.
I forgot.  How, then, might I determine which control, as layed out in IB, generated the event?

----

Uh, that would be     self in this case....

*Not only that, but General/NSColorWell<nowiki/>s don't have cells. You'll have to just call     tag on     self. However, this is still the wrong way to do this. Just connect the action of a normal General/NSColorWell, make sure the cell's action is set to be continuous, and go from there. The action will be fired whenever the color well's color changes, with the General/NSColorWell as the sender. This way you can implement just one method that handles all three color wells, or implement three separate action methods and skip tag testing altogether. --General/JediKnil*
----
General/JediKnil,

That's the first thing I tried.  The method I set up as for the action wasn't getting called which is why I began investigating this other approach.  Now I'm starting to think that I forgot something simple - a connection maybe - so I'll give it another go.

Thanks.

*When the proper technique fails, the answer is to debug it until it works, not change to something totally different and weird.*
----
Understood, and point well taken.  However for those of us who are much less experienced it isn't often simple to tell whether or not the first approach undertaken is the *proper technique*.  That is why this discussion board is so helpful and valuable.

*Right. So for reference, the target/action technique is usually the right one for this sort of stuff, although it remains to be seen exactly what you're doing....*

When I set my colorWell to an action - (General/IBAction)colorWellClicked:(id)sender , it doesn't seem to get called until a click is registered in the resulting shared color panel.  Any click in that color panel then perfoms the code in my action method.  Can I get the method to be called at the first click on the General/NSColorWell?

*Why do you need this? If the user hasn't clicked in the color panel, then he hasn't set any color yet, and you shouldn't need to change anything. What should happen in this case?*
----
Fair question.
You are correct.  That is the behavior I need.
