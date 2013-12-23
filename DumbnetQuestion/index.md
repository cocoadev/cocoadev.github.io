---
layout: page
title: DumbnetQuestion
---


I'm working on an application which requires that I first port a Linux library over to mac and then build a cocoa interface around it...
my question is this:  the linux lib is utilizing extensively the dumbnet library (ie dnet.h or dumbnet.h).  is there something equivalent to this on Mac?
Thanks in advance!


----
the answer:
libdumbnet is also known as libdnet (http://libdnet.sourceforge.net/).
However, there is already a package named libdnet on Debian based
systems, that's why it is called libdumbnet on Linux...  works on OSX

