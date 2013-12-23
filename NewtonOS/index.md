---
layout: page
title: NewtonOS
---



Don't you people remember that Apple Handheld called Newton?

*Yes. I have 2. What about it?*

I want one so badly!!! 

*So get one! They're pretty cheap on ebay (under $20 or so)... I want an eMate*

**I also have two.**

The programming environment for the Newton was uber-cool.

*I have an eMate, but I'm still trying to get stuff to connect it to my main computer... Care to elaborate (or provide linkadge) on the Newton programming environment?*

----

 **Syncronization under MacOSX**

  Look at the page for the DCL, or Desktop Connection Library, at  http://www.kallisys.com/newton/dcl/ .  This is which is intended to be a replcement for the old DILs (Desktop Integration Libraries) provided by Apple under MacOS classic.  There are two package installation programs, Escale, and Delivery.  Delivery is simpler, and may be less crash prone (Escale will regularly crash after installing a package, or when you do quit - we have no idea why).  Escale interfaces over Appletalk, TCP/IP (with or without Rendezvous), or a serial connection currently with the native Newton Dock application (so you don't have to install a custom package..  Somehow..  See the Bootstrapping Problem at http://tools.unna.org/wikiwikinewt/index.php/BootStrapping for an more throurough explanatin of this conundrum..

  For that matter, are there any hardware developers here that can do surface mount work who'd like to work on an internal serial-port/bluetooth driver for the Newton 2000 - email me and I'll tell you more about specifics.  Or people who know how to make the obsolete Interconnect Port [originally made by JAE] at a reasonable cost, so we can make audio in/out adpators for the masses?  Or do surface-mount rework to replace the internal NIC port on the 2000 motherboard with some connector that's still made?

  Escale can't do information-synchronization or backup/restore yet - that's why we need developers, but the basis is there - the Dock protocol implements arbitrary soup query operation remotely.  The full Dock protocol is documented in the 'Docking Protocol' note of the Bowels section of the Kallisys website at http://www.kallisys.com/newton/bowels/)  We could use a TDCLSoupRef class in the DCL..

  And package installation is just the beginning..  The DCL has the functionality to build packages directly on the Mac - from the ground up using frames and references (although we don't have a NewtonScript compiler yet - anybody have LEX/YACC compiler-writing experience?).  An example of this is a program (included in the CVS tree) called NESPackageMaker, which will take a Nintendo ROM file and create a package that can then be used by the Newton NES emulator, called appropriately enough (if a bit silly sounding), Newtindo, as well as an example to build Newton books from text files, given templates in NSOF [Newton Streamed Object Format].

  *We need devlopers for the DCL!!*

 ** Newton Software and Hardware**
  As for software, by far the biggest advance for the Newton was the creation of an ATA driver, which can be found at http://www.kallisys.com/newton/bowels/.  This was rather a tour-de-force of programming, as the author has told me that about 3/4 of the project was reverse-engineering how the Store interface and other things in the operating system worked.

  There is also an IrDA driver (Nitro), a beta Bluetooth driver (Blunt), and an MP3 Player (Mad Max), all found at http://www.40hz.org/ .  There are a number of web-browsers for the Newton, one of which (a text-only browser that is aparently quite fast) is found at the above site.  Others are the original (Apple distributed) NetHopper, and NewtsCape, written by Steve Weyer (found on his site at http://home.comcast.net/~saweyer/newton/newtscape.htm)  With NPDS (Newton Personal Data Sharing, at http://npds.free.fr/), the Newton can function as a webserver.  And there is indeed a wireless PCMCIA driver, which supports WEP, at http://www.ff.iij4u.or.jp/~ngc/eng/newtwave.htm .  There is no VNC, SSH, or SSL support, but this is just a matter of C++ and p-class programming..

  Sadly, Firewire and USB connectivity will be much harder, as the PCMCIA cards that support them are almost all CardBus-based, which the Newton does not support (only 16-bit).  If a small enough serial->USB chip could be found, it's possible that an internal module could be made.

  Finally, there is the recently formed Worldwide Newton Association (http://worldwide-newton.asso.eu.org/), which had it's first annual confernence, the WWNC (http://wwnc.newtontalk.net/) in Paris, France earlier this year.  The program for this year's conference can be found at http://wwnc.newtontalk.net/program/ .  The absolute highlight of the conference was the unveiling of the first Newton *hardware* emulator, written by Paul Guyot and Nicolas Zinovieff, slides of which can be downloaded from http://wwnc.newtontalk.net/program/paulguyot/ .  This is not a finished project, and is rather secret at the moment.  If there are any IP lawyers here, we might be able to use their help - although writing "emulators" has been done for years, and has been held to be perfectly legal.

----

I have the Programming for the Newton Using Macintosh, 2nd edition. Barely opened it up, still great condition :)

----

  There is a large amount of developer documentation, all of which can be found on the United Network of Newton Archives, (UNNA), here: http://www.unna.org/unna/development/documentation/ .  Also in the /development hierarchy can be found a large collection of Apple sample code projects demonstrating NewtonScript (NS), the bytecode language used to write all the user-level interface programs on the Newton.  Start with the Programmer's Guide, it will take you through writing simple programs, the NewtApp framework which allows you to write an application that has a large amount of the work done \\"for free\\", such as data storage (using soups, which are free-form collections of linked objects called frames), as well as routing (used for printing, email, etc).

  There is also a very bare introduction to how to program the Newton using C++,   C++ programming is meant to augment NS programming to speed up critical algorithms.  However, I have been experimenting for the past year with accessing the Newton's QuickDraw routines and pen input, which should allow  an application (a game, for instance) to be written almost entirely in C++ (I'm lazy, which is why I've been working on it for a year, and why it's still unpublished anywhere except for a sample program that's floating around out there).

  I am planning to start (at some point) a Newton Developers' Mailing List, which will focus specifically on NetonScript programming, both for new and seasoned Newton programmers, as well as a certain amount of discussion of more advanced C++ programming, and the writing of p-classes. (P-classes are protocol classses, which are used for hardware drivers and other things in the lower-level C++ operating system - they are a mechanism to get around the fragile base-class problem inherant in C++).  This list would be separate from the Newtontalk list (http://www.newtontalk.net), which is a rather huge sprawling discussion by now.  If annyone is interested, please contact me at mailto:js(INDIANA)witte@(at)bloomington.(co)in.(com)us .  Yes, that **is** a legal RFC 2822 address - it is an 'address with hair'..

