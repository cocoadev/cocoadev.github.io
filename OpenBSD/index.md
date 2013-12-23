---
layout: page
title: OpenBSD
---

OpenBSD is the third branch of the BSD family, and also the newest.  It was created as a fork of NetBSD a few years ago, and they now peacefully coexist.  OpenBSD, despite its name, places great emphasis on security and is widely regarded as one of the most secure systems out of the box.  It also has a reputation for being fairly difficult to install.  Its most common use is in servers that will be widely exposed to the world and require the tightest security possible. --OwenAnderson

Our Quadra 650 runs OpenBSD. It doesn't run anything under OpenBSD, but hey, one thing at a time. -- RobRix

I edited OwenAnderson<nowiki/>s comment, which might be the wrong thing to do, so I'll post here as well. OpenBSD is a fork of NetBSD, where Theo de Raadt was a founding member, later he got kicked out, for details, see http://zeus.theos.com/deraadt/coremail.html -- JonOlsson

Cocoa programmers might be interested to know how to get GNUstep working on OpenBSD.  Well, there is a port at [http://archives.neohapsis.com/archives/openbsd/2005-01/0453.html].  One caveat, NSThread will not work because OpenBSD's libobjc is mis-configured.  You can get around this by linking thr-posix.c from the GNU ObjC runtime source code (libobjc) into your application.  (You will also need to remove references to scheduling functions to get that to compile -- see [http://archives.neohapsis.com/archives/openbsd/2005-01/att-0453/libobjc.patch])  -- Anonymous

By the way: OpenBSD is not the newest fork of a BSD tree, and it was founded in 1996 (that's more than "a few" years in my book).  The name "Open" BSD comes from the fact that its development process is more public ("Open") than NetBSD's was in 1996.  Back then, NetBSD's CVS tree was not public for example, so OpenBSD would be in contrast more open than net.  -- Anonymous

