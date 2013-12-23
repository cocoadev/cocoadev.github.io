---
layout: page
title: WhenDoesControlActivationOccur
---



I'm a bit puzzled about how General/NSControls and their cells receive activation and deactivation "events" from their underlying windows. There is no explicit General/NSEvent for this, but nevertheless controls usually seem to know when their underlying window state changes.
 --GC

----

Hi, GC. I think you can try to set a breakpoint to General/[NSControl setEnabled:].

----

Please avoid General/MailingListMode on this wiki. Use a more appropriate venue such as cocoa-dev (Apple) or macos-dev (Omni Group) for Q&A, "mailing list" style posts. Also, what "state changes" are you referring to? Are you referring to the fact that controls are grayed out when inactive? This is separate from "enabled".

----

My experiments show that this is a bug, possibly one that Apple think is "intended as designed" :( Basically controls never get an activate event if they are in windows that have no title bars. (if there are such events - it could simply be that they are asked to redraw and internally they ask for the window state.). Anyhoo, it means that it's not possible to make a completely custom built window with active controls since if you don't set the     General/NSTitledWindowMask flag, controls never get activated. If you do set it, you get the system's title bar. Why these two features are so conflated I do not know, but it's really really sucky if you want to do even slightly adventurous things with the user interface.

Possibly Apple's rationale might be that if you have no title bar it would be bad to have an active window, since it could be confusing to the user which one is really active. However, it means that if you want to take on this aspect yourself (i.e. I promise that I won't confuse the user, Apple!) it still doesn't give you a tool you can use. Apple themselves may have run up against this one when adding custom views as menu items in Leopard, since those controls are shown active even though the menu's "window" is not active and has no title bar. Clearly they are willing to pull strings for themselves but not give it to the rest of us.

In tracking down this, I also found an additional bug where if you DO set the title bar flag, you get a title bar, but its height is subtracted from the content area! This means that the API itself     initWithContentRect:... is WRONG, since it doesn't use that rect as the content rect, but as the frame rect. Arrrgh! This is the sort of thing that used to P... me off about Windows, where I could be smug about how relatively "correct" Apple's General/APIs were. No more. Grrrr. --GC
