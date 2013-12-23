---
layout: page
title: CatchSystemEvents
---

I have two questions here...

I want to know how to make an app that doesn't have a GUI, and is always running in background, something like the Mac OS 9 extensions...
I also want to know how to catch system events like mouse clicks, mouse location, key downs, that happen when in the Finder...

Yet I have no idea of how these are achieved...

-- Trax

----
Try looking at application enhancers (APE's) by unsanity.

Also check out extendamac. http://extendamac.sourceforge.net/

----
Forget APE's, they're excellent for some things, but you're obviously asking about doing this stuff from a developer's point of view not a user's point of view.  First off, background applications can be set very easily.  Whatever application you're designing that you want to be a background application simply go to the active Target, select the Expert View, click on the New Sibling button and type LSBackgroundOnly.  The class should be Boolean, and the value should obviously be Yes.  
As far as your other questions go, these things can also be accomplished, but if you're asking for something specific it would be simpler to answer like your first question was.  If you're just asking in general how an application can gather the information from mouse clicks, key presses, etc., then definitely start searching Apple's developer site as well as the discussions section of the Support site.  Now, as far as mouse location goes, there is a call for the coordinates of the mouse but I don't have them on the top of my head.  Someone else that deals more with such things can be of more help.

--TriLateral

----

