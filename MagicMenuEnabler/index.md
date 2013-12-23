---
layout: page
title: MagicMenuEnabler
---

Describe MagicMenuEnabler here. I get this error on every project i run, it doesn't stop the app from running, but I don't like errors popping up.  This is the text I get


[Session started at 2007-02-20 08:59:47 -0600.]
2007-02-20 08:59:47.640 CocoaNotePad[3751] *** -[NSBundle load]: Error loading code /Library/InputManagers/MagicMenuEnabler/MagicMenuEnabler.bundle/Contents/MacOS/MagicMenuEnabler for bundle /Library/InputManagers/MagicMenuEnabler/MagicMenuEnabler.bundle, error code 2 (link edit error code 0, error number 0 ())

CocoaNotePad has exited with status 0.

As you can see, the App ran and exited ok.  What is it?  The version of Xcode im using is 2.4.1.

R.G. Easter

----

MagicMenuEnabler is crashing. If it's doing it for unmodified, brand new projects, the problem is probably with MME and not your code. Try disabling or re-installing MME.

----
It's not crashing. MME is a single-architecture binary for an architecture that the poster's program is not running in. For example, it may be a PPC-only binary and he's running as x86. That cryptic error message means that no binary for the current architecture was found, so the bundle could not be loaded.

