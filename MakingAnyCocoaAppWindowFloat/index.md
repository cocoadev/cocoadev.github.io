---
layout: page
title: MakingAnyCocoaAppWindowFloat
---

Hi,

There once was a software that could make any cocoa app's window float / stay on top of all the other ones. However, I can't find it anymore.
Now, I assume it's a carbon call or something, I'd have to get a windowRef and call some carbon function... anybody knows which one? ;)

Thanks.

----

Google is your friend. There's an instance method off NSWindow that does exactly what you want. Go forth and find it.

----

Well, I think I mis-phrased. I want to create an app that makes a window of any OTHER app floating, not the window of my app. So, the user clicks on "make floating", selects any window, say, a browser window of safari, and suddenly, that window is floating above all other windows. My app is just there to make this possible...

I hope that describes it better.

----

An input manager could do it with the aforementioned method and with equivalent Carbon calls if needed. CoreGraphics probably has a set of functions for exactly this sort of thing anyway.

----

There was Afloat at http://afloat.infinite-labs.net too, but it uses NSWindow's methods rather than windowRefs (anyone knows what's up with window groups anyway?). -- EmanueleVulcano aka millenomi

