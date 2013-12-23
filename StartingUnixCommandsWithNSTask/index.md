---
layout: page
title: StartingUnixCommandsWithNSTask
---

I use ruby from within a cocoa application to do some stuff but I realized that ruby might be installed at different paths on different computers. Some might have the most recent version of ruby in /usr/local/bin instead of /usr/bin. How do I run a unix command like it would run on the user's command line?

One alternative I have thought of is launching "which" which in turn gives me the correct path. The problem is that NSTask doesn't get all the extra search paths defined in my .bash_profile so running "which" in an NSTask will yield "/usr/bin/ruby" even though running "ruby" in a terminal will launch "/usr/local/bin/ruby".

Another alternative explored by some guy (http://www.cocoabuilder.com/archive/message/cocoa/2003/2/4/93535) is to parse the PATH environment variable and look for the command in every place but it suffers from the same problem as the previous. The PATH variable doesn't take the .bash_profile into account.

I guess I could start bash and let it run "which" (or even ruby) but this is a general problem that applies to all unix commands (even "which"!) so it should have a general solution, but does it?

/Gabbe

ps. I know that Mac OS X apps can be found with NSWorkspace
----
You just need to use a shell with NSTask.  The PATH environment variable and the search order for finding Unix shell commands are obviously properties of the shell.  You could consider a pty or using popen.  Also see the Moriarty sample: http://developer.apple.com/samplecode/Moriarity/listing5.html

