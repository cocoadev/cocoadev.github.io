---
layout: page
title: ClassDump
---




class-dump is a command-line tool which, when directed to the binary part of an Objective-C application, prints out the interfaces to that application. NOTE: That's interfaces, not implementation. That is, you can see how it's organized, but not how it works.

To see what this could be useful for, I recommend you check out the article on Bundles on StepWise:

http://www.stepwise.com/Articles/Technical/Bundles.html

----

The utility is available at http://www.codethecode.com/projects/class-dump/.

*FWIW, it's also available as a fink package*

----

Not ClassDump, but something that may be of use:
The OmniGroup has a plug-in for OmniGraffle to display a diagram (very nice!) of a nib file.  See
Interface Builder import example on
http://www.omnigroup.com/applications/omnigraffle/samples/

*Doesn't appear to be in the regular version, you have to get Pro http://www.omnigroup.com/applications/omnigraffle/pro/index.html *

----

Other possible uses are outlined here:
http://culater.net/wiki/moin.cgi/CocoaReverseEngineering

----

If you need to use class-dump with Universal Binaries on 10.3 (or if you don't have class-dump 3.1), you can use the 'ditto' command to make a non-universal binary, then run class-dump on that. Here's an example using Camino 1.0 (a Universal Binary):

    
$ ditto -arch ppc /Applications/Camino.app/Contents/MacOS/Camino ./
$ class-dump Camino


Specifying <code>-arch ppc</code> tells ditto to only copy the PPC version of the binary, leaving you with a normal, PPC executable that class-dump 3.0 can read.

----

class-dump doesn't seem to be working in Leopard (9A559). I recompiled from source but when I run it a binary, the output is just garbage. Any ideas?

----

I believe class-dump relies on otool. See if otool returns garbage or check if there is any difference with otool or the standard libraries between 9a527 and 9a559.

----

I recompiled against the 10.5sdk in 559 and it seems to work just fine for me.

----

Also available - class-dump-x which supports the new ObjC 2 ABI. http://iphone.freecoder.org/classdump_en.html

----

class-dump has supported the new ObjC 2 ABI since version 3.2 (9/2009); latest version also supports iPhone binaries, 64-bit, 10.6 protected binaries, etc.

