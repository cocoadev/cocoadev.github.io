---
layout: page
title: SystemCrashes
---

Hello, I have recently developed a program on my powerbook g3 500. I am running Mac OS 10.2.8.

The program runs perfectly on my system however I am getting some feedback from my customers that when using the program their system will suddenly slow down and and ultimately ask them to restart their Mac.

Can anyone offer me some ideas on what type of problems would cause the system to require a restart?

Thanks

----

Sounds like a memory leak.

----

To me, the 'suddenly slows down' part suggests the system is thrashing VM and eventually running out - i.e. a memory leak. I've never actually had it happen, but I suppose OSX could put up a dialog telling users they need to restart.

----
More Info:

This is really a pretty basic program. I using very few dynamically creatied objects. Most of the objects are were created in IB ie: General/NSImageView, General/NSButton, and General/NSTextField. I never actually free any of these, they just change states as the game progresses. There are how ever a lot of objects. 

I have asked my customers for more feedback and I will advise as it comes in. I am also using a couple of timer ojects which I am not using "free" to to release them from memory. The text I am learning from explains to use "invalidate" instead. I have also recently implemented code to track the high scores. I suspect the problem is either with the timers or the high score code.

----

I had some sudden problems with General/OpenGL when I updated to 10.3.3 -- you can read about them in General/SuddenPerformanceLoss

It had to do with display list creation/deletion. Of course, if you aren't using General/OpenGL then it's irrelevant. Nonetheless, I recommend some investigation with the CHUD tools. They're excellent. --General/ShamylZakariya

----

I have seen some reports that imply that 10.3.3 has a VM issue related to disk fragmentation that will cause Kernel Panics. If you have a major memory leak, it could in fact cause this to rear it's ugly head. Note: **invalidate** just stops a timer from firing, it does not free it, if you want to dispose of the timer object, you still have to **release** it.
----
You hit the nail on the head!!

Turns out the problem that was causing the kernel panic was a major memory leak related to the General/NSSound class. see the discussion General/SoundWoes posted here.
----
