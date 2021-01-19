---
layout: page
title: GNUStep
---

From the GNUstep home page [http://www.gnustep.org/]:

*
"GNUstep provides an Object-Oriented application development framework and tool set for use on a wide variety of computer platforms. GNUstep is based on the original OpenStep specification provided by NeXT, Inc. (now Apple). GNUstep is becoming more and more stable every day and is used in a production environment by several companies."
*

Essentially, GNUstep is Cocoa for non-MacOS unix systems, including Windows.

Some GNUstep links:


*GNUstep Wiki [http://wiki.gnustep.org/] has lots of GNUstep advocacy, could use lots more meaty content. :-)

*Official GNUstep Project Page [http://savannah.gnu.org/projects/gnustep/] where you can ponder what the source code to Cocoa might look like if it were developed by other people.

*GNUstep Cookbook [http://www.gnustep.it/marko/index.html]  Filled with numerous ObjC examples.



----

Note: This page expanded to facilitate the cross-pollenation of Cocoa and GNUstep knowledge and wisdom. Werd.

----

Anybody know how easy it is to write a Cocoa app on the mac, copy the source files onto a GNUstep machine, hit recompile and whatch your code run?  Or... is that another pipe dream?

----

That's not that bad.  I took a look at the documentation.  Port the GUI and a maybe a few Foundation calls that aren't in GNUStep by chance and you're off!

----

It's easier to write in GNUstep and then port to Cocoa. There are a few interesting tools out there to facilitate this, such as Renaissance [http://www.gnustep.it/nicola/Tutorials/Renaissance/node1.html]

----
Since last fall, GNUstep supports reading and writing NIBs. Renaissance is still the best option for cross-platform interfaces, though.
----
Use GNUstep with WindowMaker for that (Semi-)Authentic NeXT Experience(tm)

----
There is a fork of GNUstep called mySTEP [http://www.dsitri.de/wiki.php?page=mySTEP] that aims at making the mentioned dream real - at least for porting to Linux-PDAs (like Sharp Zaurus). It uses a MacOS X machine with Xcode as the host for a cross-compiler and a NIB translator (nib2mib).

----

There's a GNUSTEP Live CD at [http://io.ethz.ch/~tar/gnustep/]. I'm attempting to boot it with Bochs now....

*So we should check back in three days then? :-p*

hehe... still booting.... **update, the next day:** It failed 3 times with various errors. I'm giving it up for now.

----

I was wondering if anyone could clarify this seeing as I didn't see any clarification when I was looking at the LiveCDs, earlier:  what OS is it running?  Since GnuStep is just a library, a *Live CD* sounds kind of odd.  Can anyone clarify this?

*It uses Morphix Linux [http://sourceforge.net/projects/morphix/] which I believe is based on Debian. The ultimate goal is to use HURD, to create a purely GNU OS.*

----

A made a (scientific) program in cocoa and, as expected,  I was asked for a windows version. I decided to try gnustep instead of rewriting everything in cplusplus.
But after a lot of effort (and a lack of knowledge of windows) I can not even produce a hello world program. Does anyone know of a manual for starting GNUstep on windows ? The above mentioned Live CD is for Linux.  PaulCD.

----

Try http://www.gnustep.org/resources/documentation/User/GNUstep/README.MinGW, and good luck.

----

I do think that it's very feasible to write a cross platform app with GNUstep/Cocoa, but you have to start with GNUstep (not Cocoa, since you will end up using Cocoa-only features) and don't use Interface Builder or Gorm.  Instead, there is a tool called GNUstep Renaissance[http://www.gnustep.it/Renaissance/] that will let you specify a UI in XML, stick it in your app bundle, and then have an extension to NSBundle turn it into objects for you.  It's far from complete at this stage, but it's not bad to work with.  In fact, if it ever gets developed further, it would make a pretty bitchin' portable alternative to NIB files.

I'm a Unix guy (I use OpenBSD on my laptop, Debian GNU/Linux other places, some Solaris), and I don't own a Mac that is capable of running OS X.  I've been writing a few small applications with GNUstep and I absolutely love it.  This wiki is also a good source for information when I want to know how to do certain things in NeXT, but of course some of the topics it discusses are Cocoa-only and have not been implemented by GNUstep.  I kind of wish more Cocoa programmers were more aware of GNUstep.  I don't mean to start a wiki flame war, but so many of them don't realize that the classes they are talking about can be used elsewhere, and have been many years before OS X even existed, not just at NeXT or in GNUstep but also the various third party vendors who shipped OpenStep implementations (that used to include Sun Microsystems, back in the day).  From that point of view I sort of cringe when I see something written about an NS* class with a clear Mac bias.  But again, that is not meant to be taken as a harsh criticism.  ;-)  My point rather is, GNUstep/Cocoa would make an awesome cross platform dev framework, if only more people knew about the former.  The two communities could benefit from each other as well.  (Mac guys know about good user interface design, Unix folks know lots about, well, Unix, and doing things portably.)

-- Anonymous.

----

Portability is quite important these days.  Unfortunately this route (GNUStep/Cocoa for portability)means you either can't use or need to reinvent features that the rest of the Cocoa community has had for a year or more.  When I was in college this would have been very interesting to me (for obvious reasons) now that I'm making a living writing a Mac OS X app it's not (for obvious reasons).  GPL makes an unfortunate split in between the academic and business worlds.

-- Anon

----

Ludovic Marcotte, the author of GNUMail, wrote an article about cross-development using Renaissance, available at http://www.linuxjournal.com/article/7102

----

*GPL makes an unfortunate split in between the academic and business worlds.*

Just for the record, GNUstep is LGPL.

----

As of version 0.11 of the GUI package (AppKit), GNUstep can now load NIBs made for Mac OS 10.2 or later.  This should make GNUstep a lot more compatible with graphical Cocoa apps.

----

An example of portability using GNUstep is eggPlant (http://www.testplant.com/), that uses GNUstep to run on Windows. Read:

http://www.testplant.com/wp-content/plugins/download-monitor/download.php?id=30

- German Arias.

