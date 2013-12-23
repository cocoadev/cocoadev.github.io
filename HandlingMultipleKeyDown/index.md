---
layout: page
title: HandlingMultipleKeyDown
---

GameKeyBoardHandling is a generic solution that supports an arbitrary number of simultaneous keys. It requires no Carbon or magical run loop hacks to make it work. In fact it's pure Cocoa and requires less than 75 lines of code. No offense to the Omni Group, but that I<nowiki/>nputExample is a LOT more complicated.

--AlainODea
----

Okay, I have a custom NSView, nothing very special. I overrid the keyDown method with mine so I can handle key events. It goes like this:
    
- (void)keyDown:(NSEvent *)ev
{
    if (ev characters] isEqualToString:@"w"])
        doSomething;
    if ([[ev characters] isEqualToString:@"s"])
        doSomething;
    if ([[ev characters] isEqualToString:@"d"])
        doSomething;
    if ([[ev characters] isEqualToString:@"a"])
        doSomething;
    [self setNeedsDisplay:YES];
}


Good news. It works! Well, I had to search this site a bit, but I found rather quickly that the following method is essential if you want the event to be catched by the view. Actually, I don't know if it's the only way, but since it works, I won't put...
    
- (BOOL)acceptsFirstResponder
{
     return YES;
}


Now, what I would like to know is how to handle multiple keys down at the same time. For example, if I press "w" and "d" at the same time, a specific method is called. Anyone has an idea about this problem ? -- Trax

----

To check for multiple keys, wouldn't just doing if ([[ev characters] isEqualToString:@"wd"]) work? -- [[KentSutherland

----

Sadly not.  Each key event (generally) only refers to a single key press, so you'll get -keyDown: invoked once for each character, not once with multiple characters.  You can still check by overriding -keyUp: too and checking if you get a bunch of key down events before you get a key up event, which would signify that multiple keys are being pressed at once. -- Bo

*Not only that, how are you supposed to know what order the keys are pressed? ("dw" should match the same keys as "wd"). And how are you supposed to check for unprintable characters, like delete and the function keys? (Yes, I know that there are Unicode keys for some of these).*

----

But be aware that not all keyboards support arbitrary key combinations (to save on the wiring). In fact holding down some keys may block others...
----

Well... Okay... Then, start up any game you have on your computer that allows more than one key down. How are they doing? I mean, a game that allows only one key down at a time is not funny, especially when you want to make diagonals, and fire a laser at the same time, for example...

-- Trax
----
In fact I recall Mortal Combat II on the PC in multi-player mode, and often one player could "block" the other by holding down the proper keys. So no, I am not making this up... btw: I think most games use the dedicated qualifier keys (shift, control, alt, command) for "additional" actions (like run fast, shot, go sideways etc.), since these keys should not be subject tot he problem I mention.

Take a look at OmniGroups game source exampes at this page http://www.omnigroup.com/developer/gamedevelopment/gdc2001/. Look at I<nowiki/>nputExample on the disc image, I think that's the one. That should help you, maybe :) -- KentSutherland

You have to override the run loop to get this exceptionally basic functionality? That just plain *sucks*. -- RobRix

----

You shouldn't have to override the run loop for this -- although many games have their own run loop for other reasons. To keep track of multiple keys, you can just create a BOOL instance variable for each key you want to keep track of. Some games do this for every key and put it in an array. Set the boolean to YES in the keyDown: event and NO in the keyUp event. You can then check for a combination of keys any time you want. BTW, if you are creating a game, you may want to make sure the key down event isn't a repeat     if (![ev isARepeat]). Hope that helps.

-- RyanBates

From what I've seen, this doesn't alleviate the fact that the system only sends the events for one key. E.g. if you're pressing i and then press j, you don't receive additional messages for i, even though you may want them to increase something such as acceleration. You could check the amount of time since -keyDown: in -keyUp: since you'll receive those properly, but still. Jumping through hoops. Arr.

-- RobRix

