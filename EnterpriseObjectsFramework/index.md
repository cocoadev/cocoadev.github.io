---
layout: page
title: EnterpriseObjectsFramework
---



Basically, EOF sits between a database and your application. Your code can treat database content as if it were plain objects by calling EOF objects to do the translation for you.

If you ever change the underlying database, you only have to change some code in the EOF layer to update the Object<->Database mapping, the rest of your code remains the same.

The EnterpriseObjectsFramework is alive and well! It is currently sold as part of WebObjects. However, there are no technical reasons why it can't be used in a desktop Cocoa application. (Political and licensing issues, however, are another story...)

Documentation is available here:
http://developer.apple.com/techpubs/webobjects/webobjects.html

----

EOF in WO 4.5.x is severely broken on Mac OS X, and in WO5 it's some evil Java thing, so it's arguable as to whether it's alive and well or not.

-- FinlayDobbie

----

By "evil Java thing", I believe you mean that EOF 5 is written in Pure Java. Sorry, I like this -- it means EOF can go places it couldn't before (like Linux and FreeBSD).

-- JonathanWolfRentzsch 

----

There is an implementation of Obj-C EOF available for GNUStep.  I've never managed to get it to compile under OSX, but I can't see it would be that much effort to do, as it claims to be portable over GNUStep, OpenStep and NeXTSTEP foundation libraries. *It has been done; see GDL2 discussion below.*

----

Well EOF (Objective-C) is only partly severely broken in Mac OS X. The EOInterface palette doesn't work anymore. EOAccess and EOControl work well still . 

-- Nat!

----

EOF is a very cool technology, being a database abstraction framework and all. But if you're running on Jaguar and you need, say, a database of people, and you're content to live with a couple of restrictions (using Apple's implementation and having it machine-local), you might be able to use the AddressBook framework to your significant advantage.

Under Jaguar, both Mail and iChat use AddressBook. Some other candidates might be a VerticalApplication which uses customer-centric records, a program which allows a teacher to see his/her students' marks at a glance, or other similar programs.

Remember: it's not gonna be mission-critical fast. And if you want to have the database be on a different machine from your application, you're going to be setting that up yourself (perhaps RendezVous can help the configuration). But it might help you, and who knows? It might be expanded in the future, too. -- RobRix

----

Well, Thanks to the enormously good work of quite a lot of people, whose names I won't mention but I know who they are ;-), I currently have gdl2 (the GNUStep EOF clone) working on my powerbook and happily pulling stuff from Postgres.  Needless to say, I'm a happy, happy, bunny.  It misses quite a bit of stuff still (notably EOModeler), but it works.  Code generation through EOGenerator  is good, though, so all you need to do is either build an EOModeler replacement or hack your models by hand (time-consuming, but less painful than rolling your own database code)

To do it, you need to compile the GNUstep-base package as a framework (relatively easy with some thought) and then compile up gdl2 likewise.  I may put a binary package together if I get the time.  Watch this space.

TufTy

*A somewhat dated (Mar. 2008) port of GNUstepWeb (WebObjects clone) and GDL2 for Mac OS 10.5 may be found here: http://freshmeat.net/projects/gdgswebforosx *

----

Currently, the best Objective-C replacement for EOF seems to be AJRDatabase:

http://sourceforge.net/projects/ajrdatabase/ (for the latest CVS release, recommended)
http://www.raftis.net/~alex/source.html

It also contains a very useful adaptor for postgresql

brunobl

