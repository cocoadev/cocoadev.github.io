---
layout: page
title: ParallelBuildsInXcode
---

(I hope the title is not confusing: this is not about *distributed* builds)

I would like to do the following in Xcode:


*(1)

Have in parallel two build histories for the same product, one for Development and one for Deployment; because every time you switch, you have to rebuild everything, and sometimes, it is useful to switch to Development to run the debugger after minor changes and not have to rebuild everything for that, and then again to build the 'optimized' binary after those changes

*(2)

Build two different products from one target; for example, have a build tuned for G5 (with specific code; ie the flag -mcpu=G5) and a build for the other CPUs; everything is the same in the two builds, but a few flags, and of course the product



I know in both case, one could use two targets. But this means you have to repeat every changes you make to one target to the other (like linking a framework, adding a source file to the target, setting other flags,...). For problem (1), it also mean I have to switch target when switching build styles.

Maybe all of this should go in XcodeMostWanted ;-)

thanks for sharing your ideas and tips about this... --CharlesParnot


Somebody just removed this post from CocoaDiscussions. Thanks, anonymous cleaner!
I should have taken care of that, having myself started the post. Anyway, it is actually now fixed in Xcode 2.2 (or maybe before), with the Configurations system. Xcode keeps separate Build history for different configurations, so I now get exactly what I want (and what I guess many people wanted, if they implemented it). --CharlesParnot

