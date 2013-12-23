---
layout: page
title: ProgrammaticallyControlExpose
---

So, just as the page title suggests, how can I programmatically control expose? When my application is activating another, iIt'd be nice to have (based on user-preferences, of course) expose activate, then automatically choose the target app's main window as if the user had exposed all windows and chosen the app's main window themselves. Arguably good idea, but that aside, is it possible to accomplish this in a non-clunky way?

----

One must keep in mind that Expose' is strictly a user-activated function and there's no way that I know of to control it programmatically. You should simply activate the target application using NSWorkspace's methods in my opinion, without "hijacking" Expose' from the user. --l0ne

*Not knowing a direct way to control it is a fair-enough answer, but as I said above, with all user consideration aside (as a good prefs option will allow), I'm just interested in figuring out how - if it's at all possible.*

----

*Fake events?*

----

I don't believe it's possible in any supported way. Nor in any non-annoying way ;) How about putting up a BezelServices-type overlay window as you switch?  *Nnnoooo, I'd rather find a MORE ANNOYING way. You people are no help! :-) *

----

It is possible, if you so wish, to control Expose' by making use of fake events, for instance via GUI Scripting (fake Expose' keypresses via 'key code' and you're set). Of course, this involves a. knowing the key to press and b. activating Expose' keypresses programmatically, which might probably be done via defaults.

