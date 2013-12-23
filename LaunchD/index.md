---
layout: page
title: LaunchD
---

launchd is the Apple-preferred way to launch background processes on OS X 10.4 or later, termed "Launch-On-Demand Daemons and Agents". launchd is in part a replacement for using inetd or cron.

Under launchd, a daemon/background process does not need to fork/exec, call daemon() or do many other daemonish housekeeping things. It just does it's work and quits, to be launched again when the next scheduled run (cron-like) or registered port is opened. Although launchd has an API in launch.h, this is for use by daemons running under launchd and not required.

A plist (in one of several directories including /Library/LaunchDaemons ) tells launchd how to run your process. To register (or unregister) your plist (and thus your process) with launchd, you run the command-line tool launchctl - I can't find any API for doing this programmatically. Also, at system startup, all plists in the special directories are registered.

launchd is covered in the Dalrymple/Hillegass book, "Advanced Mac OS X Programming" ("2nd" edition, 2006).

http://developer.apple.com/documentation/MacOSX/Conceptual/BPSystemStartup/Articles/LaunchOnDemandDaemons.html

Also: http://developer.apple.com/technotes/tn2005/tn2083.html

Gotcha #1: It is possible to put your own private keys in the plist. However, launchctl crashes if you include a <date> value (as of 10.4.8).

Gotcha #2: There have been various issues with launchd since its introduction in 10.4. My impression is that its fairly safe to use as of 10.4.6.

Gotcha #3: As of 10.4.7-8, launchctl (load command) spins off something which runs for 30-60 seconds after launchctl returns, then prints "Workaround Bonjour: Unknown error: 0". While this doesn't seem to be hurting anything in my usage, there are reports of services being interrupted during this 30-60 second period. For more info, Google the error message.

-- PaulCollins

