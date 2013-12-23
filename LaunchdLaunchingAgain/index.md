---
layout: page
title: LaunchdLaunchingAgain
---

I need to run a serveur on a port < 1024 so I need root permissions.
I wrote a tool to launch launchctl and load or unload my Launch Daemon (/bin/launchctl  load -w /....)
Here is the problem :

 *- my tool launch as root from my PreferencePane
* - launchctl launch as root
 
*BUT
* - a new unprivileged instance of launchd runs as my username and try to load my server but it fail due to the port lower than 1024

In fact, it seems that launchctl does not connect to launchd (pid 1) but connect to a new instance of launchd

If i don't use a tool and launch lauchctl directly from my PreferencePane via NSTask, launchctl runs as root but same effect about launchd !

What can I do ?
Thanks a lot
S�bastien Gallerand

