---
layout: page
title: CocoaLoadFromCarbon
---



I'm writing a plugin for a carbon app.  my plugin is a mach-o carbon framework plugin ( C ).  that plugin needs to in turn launch a Cocoa application and be able to pipe data to and from that cocoa application.  What looks ideal to me is the NSTask class in cocoa, however I need something in the carbon lib.   is there such a thing?  thanks in advance for any help and suggestions!

|K<

----
I can't help myself, but you could try asking on CarbonDev concurrently to see if they know.
----

Carbon has no equivalent of NSTask, but you can also access POSIX APIs where the equivalent is the standard fork/exec procedure, which should be covered in many UNIX programming books and tutorials. However, it's a bad idea to launch an OS X GUI application in this manner. A better idea might be to launch the application with LaunchServices and communicate using something like CFMessagePort or Apple Events.

----

Try using pipes to communicate between the two applications.  These are explained in the relevant man pages:

*man 2 pipe
*man 3 popen
The former is (obviously, being a system call) lower level, and so your best bet would probably be to use popen (and pclose).  The information contained in the man pages should be enough, but I'm sure you can find more information pretty easily if you need it.
- J. Granby

