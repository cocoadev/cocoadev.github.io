---
layout: page
title: ScreenSaverProblemsWhenPasswordRequiredToWakeFromSaverOrSleep
---

I use something like TransparentScreenSaver  [1]

for my screensavers - but with Mac OS X 10.3.x (Panther) something broke.

Transparency will now NOT work if System Preferences -> Security -> "Require password to wake this computer from sleep or screen saver" is set. If not set, it works fine.

Did anyone else run into this problem or can you point out what's the difference with 10.2 or what ScreenSaverEngine changes if it is run with password protection?

Thanks in advance,

HeikoKretschmer
----
[1] great for safely monitoring servers[2]!

[2] http://www.wurst-wasser.net/Projects/Download/LoadSaverFolder0110.sit

or http://www.wurst-wasser.net/Projects/Download/BlankScreenSaverFolder0110.sit

----

*Transparency will now NOT work if [password] is set.*

It seems intentional; perhaps fallout from screen saver password improvements in 10.3. I couldn't find anything about that on Apple's support web site. -- MikeTrent

----
Thanks! - I filed a bug about it. Hopefully they will fix it soon. HeikoKretschmer

----
Unfortunately the problem still exists in X.3.3. In worst case, this is by design. Does anybody have a workaround? - HeikoKretschmer
----
Great, Now we have bug reports that our OS of choice is TOO secure. ;-) If the required password is set, the screen-saver should NOT be allowed transparency in the first place.
----
Generally I agree. But since I run several Mac OS X Servers which I like to monitor while they are securely locked I'd really like to have this configurable. The admin of the Mac should be able to decide whether to use transparency or not. - HeikoKretschmer

----

Why do you need to look at the screen to monitor them? Use something like NetworkProbe [http://www.objectplanet.com/Probe/] or just ssh. I agree with the poster above - this should not be 'fixed'.
----
Because even the dumbest teammate is able to monitor a server that way. :-) Seriously: I like to have the information coming to me instead of "collecting" them. If I must actively do something to get informations I simply don't do it (often enough). -- HeikoKretschmer

*So script it, write a cron job to send you email or something. What exactly are you trying to monitor? Just cpu load?*
----
I am not familiar with the product, but would Remote Desktop do anything for you? I was also under the impression that a single OS X Server could monitor other servers, or even an OS X client with the server tools, Server Admin and such... I am reaffirming my vote, not to fix.
----
I do have ARD, which is very helpful for fixing simple problems or guiding users. -- HeikoKretschmer

