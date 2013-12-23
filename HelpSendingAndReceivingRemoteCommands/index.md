---
layout: page
title: HelpSendingAndReceivingRemoteCommands
---

Describe HelpSendingAndReceivingRemoteCommands here.

I have several devices on my network which I need to log into to check a few stats.  It gets old very fast logging in with telnet, giving the commands, and reading the results for each device.  What would be the best method for adding a GUI as to just bring up and see what each device is doing?  I really only need help with the networking section.  Wrapping was what I tried first, but can not figure out how to send commands within Telnet.  All ideas are welcome!!


----
Start with man rsh and man ssh.
Depending on the type of remote system and what stat you need to gather, sockets to an open port might be the way to go.

----
Looked into them....doesn't seem like Wrapping would work that way.  Can only log in, not issue commands in one line.

