---
layout: page
title: PivotingMonitors
---

There are a number of pivoting monitors out there, and in fact the proportion of pivoting monitors is becoming larger due to more attention paid to ergonomic mounts.

----

*do you have any actual statistics to back up this claim? the last time i saw a pivoting display, it was a Radius display connected to a Centris 650. (Radius later morphed into Portrait Display Systems.) *

No third-party statistics I'm afraid, but with LCDs it's now easier to add pivot functions.  A few models out on the market today include assorted Samsung LCDs (173B/P, 710T, 910T),  Iiyama LCDs (4332, 5131), AOC LCDs (LM727/9, LM929), HP LCDs (L1925, L2035), NEC LCSs (1980SX), and Dell LCDs (1504FP, 1901FP, 2001FP).  I think that covers most of the major manufacturers, and I bet most of the others support it as well - other than Apple.  I only have personal experience with the Samsungs, the rest are from five minutes Googling; but with LCDs, pivot/portrait mode only really requires a slight mounting change, so it's becoming more widespread.  Note that several of these monitors aren't hugely expensive for what they do - the pivot function is essentially free.  Samsung 710T/910T and Dell 2001FP especially are seen as good buys for the quality of the panel alone.

----

Unfortunately, there's no Mac OS X solution in place for pivot monitor support.  Under Mac OS 7/8/9 a company called Portrait Display Systems used to provide a software solution called Pivot Pro, but no OS X support has ever been forthcoming.  Users of a non-OEM Radeon 9800 Pro apparently also have access to ATI's VersaVision solution, which allows pivoting - but no-one else has access.

Now, there's two ways I can think of to allow pivot support.

Firstly, the hardware route.  This is essentially done for free but requires firmware support.
Secondly, the software route.  This is what Pivot Pro reportedly used, and essentially rotates the screen before performing the final drawing stage.  This would be the only option open to us... however, as far as I know such operations wouldn't suffer anywhere near the performance hit as they used to.  Would it be possible to rotate the screen buffer before drawing, using a modification process similar to what I'd imagine the Accessibility Zoom and Invert do?

This would require several steps:

1) Provide Mac OS X with different resolutions and ratios, essentially 3:4 or the widescreen version thereof.  I'd imagine support is pretty much there already, what with multiple monitors and widescreen mode being so well supported.  So the only problem would be how to list that as a 'supported' resolution.

2) Detect when the monitor is pivoted.  This may not be possible, and a software switch would have to occur.

3) Rotate the on-screen buffer before draws.

Does anyone think this would be feasible without firmware support?  I'm particularly wondering about [1]...

----

Actually, if you have Tiger, you can go to "Displays" under System Prefrences, and choose an option under the "Rotate" drop-down box. It's not based on any particular monitor, so it will work on anything. But if you click on it when the monitor isn't rotated, it becomes difficult to move the mouse - you would have to tilt your head to the side. It's pretty neat. If you have an Apple LCD with a wall mount, and you mount it vertically, you could use this feature, and it would look pretty neat.

*But not with all hardware.  Lamer video cards won't let you pivot.*

Do you have an example? I haven't seen evidence of this. Any system I've seen that had the juice to run Tiger was also able to do this trick.

*Doesn't work on a GeForce 5200FX-based iMac G5. (There's simply no "Rotate" option.)*

I don't have the option on my iBook (not that I'd expect it) so there's another example of a machine capable of running Tiger that can't do it. I just checked my G5 w/ 5200FX and it lacks the option as well...lamer video card - check!

*Same with the Ti800 I use to make a living with.  Not everyone runs the latest and greatest hardware*

Anyone with a semi-recent ATI card who doesn't already have a Rotation option should be able to get one by downloading the ATI Displays program. It works with Apple-provided cards as well. I was able to use this to rotate my display in my 15" Aluminum PowerBook. The app also adds some cool OpenGL override settings, and is worth checking out.

