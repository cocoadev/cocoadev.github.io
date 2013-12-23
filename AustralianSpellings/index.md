---
layout: page
title: AustralianSpellings
---

Quite often whilst coding in Cocoa, I find myself spelling such classes as NSColor and NSNotificationCenter in Australian English form. i.e NSColour and NSNotificationCentre. Which makes Xcode angry. Is there a way to create some sort of alias between NSColor and NSColour? So say if I write NSColour, Xcode will interpret it as NSColor?

----

I do the same thing (Canadian English, but same spellings) occasionally. Lately I've just taught myself to type the framework spellings when I'm using the framework, but in the past I've used simple typedefs to localize the type names:

    
typedef NSColor NSColour;
typedef NSNotificationCenter NSNotificationCentre;


This won't do method selectors (e.g.     colorWithCalibratedWhite:alpha:), though, and using e.g. a     #define for each selector would be a bit of a pain, so I'd recommend just training yourself to the framework. Easier in the long run.

N.B. It's not Xcode that's getting frustrated, it's the compiler. Your syntax highlighting might not be as nice with these typedefs as with the framework types.

-- RobRix

----
The best thing you can do is train yourself to just use the spellings as present in the API.  Otherwise your code is not portable to others in your team (or those who are used to/prefer the canonical API spelling), and you can't do things like "find the defition of this symbol" or "look up in documentation".

----

Although I agree that "just getting used to it" is probably better in the long-run (Apple's US-centric view is reasonable given their location), you can also use     @compatibility_alias instead of     typedef, which is meant for this sort of thing (and probably then works with class methods, which the other one wouldn't do). Unfortunately, the syntax is reversed: the alias comes before the class name. --JediKnil

    
@compatibility_alias NSColour NSColor;
@compatibility_alias NSNotificationCentre NSNotificationCenter;


----

Brilliant find. I had no idea this existed. -- RobRix

----

On a related note, I also use Australian/British English in everyday life, so while I tend to stick with NSColor, etc. for the built-in classes, I often use e.g. GCColour for classes of my own, even if ultimately they are intended for the general community. I wonder what others think of this? Is it a problem to encounter non-US spelling in 3rd party classes? --GrahamCox

----

As an American, I can state authoritatively that yes, encountering non-US spelling is quite simply unacceptable. Silly foreigners.

In all seriousness, if you're releasing things for public consumption, one could argue you should stick with the API's standard. Of course, one could just as easily argue that if you're giving something away for free, the rest of the world can just deal with the spelling. If you *really* want to spark debate, ask how NSTomato should be *pronounced* ...
----
I think that we can all agree that english is a stupid language on its own. Too many variations, variables and once only instances and situations on how words are to be pronounced, spelt and used in conjunction with each other. Particularly when the word is identical. However it does make it an easy source of humour :)

----

Let me correct that for you: spelt == spelled and humour == humor.  :-D
----
-- Not so, Humour is spelt that way downunder!

----

I use Canadian English spelling for my source; it will probably be available publicly eventually, so we'll find out if it's a problem then (: -- RobRix

