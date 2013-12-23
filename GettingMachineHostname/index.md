---
layout: page
title: GettingMachineHostname
---

Anybody know if there's a simple way to get the machine's hostname that my cocoa app is running on?

Have a user management program that logs them out when they're done and what not that gets the username by NSUserName() or something but can't seem to find a simple solution for hostname, woudl hate to have to fire NSTask just for that...

anybody got any ideas?

 -- JeremyK

----

    [[NSProcessInfo processInfo] hostName];  -- Bo

PS or     [[NSHost currentHost] names]; if you need all of them.

