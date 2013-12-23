---
layout: page
title: NSColorStrangeness
---

I don't have much experience with NSColor, and am getting some strange crashes when doing simple things with it.

I want to have two NSColor objects set to colors of my choosing. In my header I declare these two objects:

    
	NSColor *borderNormal;
	NSColor *borderHighlight;


Then in my init I set these colors up:

    
		borderNormal = [NSColor colorWithCalibratedRed:0.8 green:0.2 blue:0.2 alpha:1.0];
		borderHighlight = [NSColor colorWithCalibratedRed:0.5 green:0.2 blue:0.5 alpha:1.0];


And then I use them as follows (in the drawRect method of this NSView)...

    
if (allowDrag == YES) [borderHighlight set]; else [borderNormal set];


But I get a hang when changing the color. No errors at compile time.

Interestingly, if I switch the setup of the colors to something a bit more standard such as:

    
borderNormal = [NSColor blueColor];
borderHighlight = [NSColor whiteColor];


...then it works fine...

I am confused!

Any help much appreciated!

- Peter

----

Try retaining the colors in init (and releasing them in dealloc, of course). The NSColor objects returned from blueColor &c are probably always available from AppKit, but the ones you create yourself aren't.

----

Offhand, in my limited understanding of autoreleased objects, I don't see why the NSColor returned from [ NSColor blueColor }
should have any more staying power than one you create yourself through colorWithCalibratedWhatever.

Unless of course [ NSColor blueColor ] and its brethren return some sort of a constant. Someday I will investigate...

---- If you call retainCount on [ NSColor blueColor ] for example, you get UINT_MAX, which indicates that the object never gets released.
---- The color you get back from colorWithCalibratedRed:green:blue:alpha: is autoreleased. It's a convenience constructor, and by definition it's autoreleased. You need to retain it to keep it around.
----
When you get an object back from a method like     [NSColor blueColor], contrary to what you might think, it is *not* guaranteed that it is an autoreleased object. What is guaranteed is that you are not responsible for releasing it. This can be done with an autoreleased object, or by simply providing an object that's sitting around somewhere. If it's the second case, you can get lucky and have things work even though you don't retain the object, but you should *always* retain an object coming back from such a method, if you need to hang on to it, for correctness.

---- You should do this in init:

    
[borderNormal retain];


and in dealloc:

    
[borderNormal release];

