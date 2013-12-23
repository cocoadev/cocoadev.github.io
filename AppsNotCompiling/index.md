---
layout: page
title: AppsNotCompiling
---

I just reinstalled Developer Tools and none of the given examples or anything that I create on my is compling. The complier gives a Zerolink error. I am running Mac OS X 10.3.5 with X-Code version 1.0. I don't know whats causing this so any help will be appreciated.

----

The developer tools are tied pretty strongly to the OS version - I'd say X-Code 1.0 is too old for you.  Download the latest version from ADC.

----
Also, double check to see that you have the perms to modify the dir contents of the examples in /Developer/Examples/

*I agree with this. Better yet, you should repair permissions on your whole system (as an administrator, or while booting from the OS X installation disc) after installing Developer Tools. Many people repair their permissions after any 'installation' (versus copying an app to the Applications folder) in order to avoid all the weird problems users can sometimes encounter. -jln*

----

Put     diskutil repairPermissions / in your root's crontab and you won't have to worry about it.

*This doesn't work well for Powerbook users as it can fire off at the most inopportune times (low battery power, etc) or not at all (typically asleep). -jln*

you could, of course, write a script to check if you were on battery power before calling diskutil. and scheduling the cron job for a time when the PB is *usually* on is good enough; the world won't end if it's missed once or twice.

*Ugh. Man, your system must look like a mine field! ;-) *

The world won't end if it's missed forever. I have never run Repair Permissions on anything resembling a regular basis in the years I've been using OS X, and I never had a problem happen because of it. Permissions don't just randomly break, something has to actually break them, and if you avoid that problem then you don't need to repair them.

*Yeah, the problem is that Installer.app packages can easily break permissions on stuff they shouldn't even touch if they aren't careful.  I don't know about that minefield comment, that sounds more appropriate to disk fragmentation.*

----
 
Sorry for replying so late. I downloaded X Code 1.1 and that solved the problem. Thanks for the help.

