---
layout: page
title: LibFoundation
---

libFoundation is a free, open-source implementation of the FoundationKit of the OpenStep specification, and also contains some additions made in OPENSTEP/YellowBox/Cocoa since the original OpenStep specification was published.

It's not nearly as up to date as GnuStep, the Free Software Foundation implementation of OpenStep, but I've found that it's easier to compile it than GnuStep. GnuStep creates its own somewhat NeXTSTEP/OPENSTEP/Mac OS X-like directory structure in /usr/GNUstep, stores its libraries and headers in a configuration similar to Mac OS X Frameworks, and requires GNUmakefiles to compile, while LibFoundation is simply a library stored in /usr/[local/]lib and headers in /usr/[local/]include.

Its SourceForge project homepage (which indicates that it hasn't been updated since 1999) is at http://sourceforge.net/projects/libfoundation/ .

----

See Also http://libfoundation.opendarwin.org/ 

OpenDarwin's libFoundation is a light-weight standalone library which implements a large portion of the OPENSTEP specification and is designed to work with both the GNU and NeXT/Apple Objective-C runtimes.
OpenDarwin's libFoundation is based on the original libFoundation, written by Ovidiu Predescu, Mircea Oancea, and Helge Hess.

