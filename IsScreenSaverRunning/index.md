---
layout: page
title: IsScreenSaverRunning
---

I would like to detect when the ScreenSaver starts and stops within my App (which is not a screensaver) - is there some obvious API similar to NSWorkspace notifications for didwake and willsleep, or do I have to look at the lists of processes?

----
In answer to my own question - the following distributed notifications are broadcast (in the following order) by the ScreenSaverEngine application:

* com.apple.screensaver.action (with userinfo {action=1})
* com.apple.screensaver.didstart (no userinfo)
* com.apple.screensaver.willstop (no userinfo)
* com.apple.screensaver.didstop (no userinfo)

- RbrtPntn

----
Just out of curiosity, why do you want to know this? The only thing I can think of is if you have some animation running that you want to stop during the screensaver to save CPU cycles. But of course you can make a screensaver which still shows the desktop, so that might not be a good idea, although there probably aren't many of those around in practice.

----
I wanted to be able to give informative feedback on how/why/when the user is active/inactive. Shameless advert for app (delete if off-topic) - HPULoad.

----
That's an interesting approach but I'm not sure how effective it would be. One person could have his screensaver set to come on after one minute, and another person could have his set to come on after one hour, and it would be confusing. It seems to me that tracking idleness based on a separate idle time and using the approach in GettingSystemIdleTime would be better.

----
But of course, and I do. The objective is more to loosely classify what our idle user may be doing - and in this case they may be watching a screen saver.

