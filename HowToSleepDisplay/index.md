---
layout: page
title: HowToSleepDisplay
---



I am still learning the basics of Cocoa, and I want to build a simple menu extra that puts the display to sleep. 

I've found a shell the shell commands dim that allows me to set how long to wait before the display sleeps, but the lowest value it will go to is 1 minute. Is there a way to have it sleep immediately?

-Adam

----

After reading pmset.c I think that IOKit takes the value in minutes.
Another related problem is that iTunes seems to stop playing when the
displays goes to sleep - kind of weird for a MediaCenter like setup.

----

Adam says: The terminal command pmset also takes the value in minutes, but the lowest it will go is 1, not 0 or .1 etc. 

----

See IODisplayWrangler in IOGraphics. References:

* http://developer.apple.com/documentation/DeviceDrivers/Conceptual/WritingDeviceDriver/IOService/chapter_4_section_5.html
* http://lists.apple.com/archives/darwin-development/2002/Feb/msg00676.html


----
It's been done: http://linestreet.googlepages.com/sleepdisplaywidget . If you're still curious you can open the widget up and look at the JavaScript to see how they did it; apparently there's a magic "sleep immediately" number for pmset: 0x6666667. Since I reverse-engineered this from Line Street's program (albeit not hidden or anything), you should give them credit if you decide to make a competing menu extra or something. (They have a widget and an application). --JediKnil

