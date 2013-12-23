---
layout: page
title: HowToCreateDaemon
---

I want to create a daemon process that launches a specific application at regular intervals. I need a step by step procecdure to create a daemon.

 I tried with creating a plist file and placed it in LaunchDaemons folder. I didn"t get it.

---Thanks and Regards

----

CreatingDemonAndGettingSystemTime

----

I tried it. But it crashes.
I have tried to launch the daemon at the system start. I created the plist file and placed it in  /System/Library/LaunchDaemons.

It gives the following execption. 

Exception:  EXC_BAD_ACCESS (0x0001)
Codes:      KERN_PROTECTION_FAILURE (0x0002) at 0x00000000

Did I went wrong ....

----

It means at some point you dereferenced a null pointer. That immediately caused the crash.

