---
layout: page
title: DBFrontEndDesign
---

I'm a long-time mac user. My programming has been mostly for web or server-side: HTML, javascript, php, perl, PostgreSQL. I've also developed a database app for a client in MS Access.

I'm wanting to find a development environment for DB apps on the Mac (if possible, a x-platform IDE). I've researched Filemaker (though I haven't been able to get a copy of v 6 to play with), and from the threads it seems FM is quite limited as a programming language; MS Access seems to be much more flexible and powerful.

I'm currently leaning toward RealBasic; looks like it can do everything I need quickly, but I'm wondering about app speed and flexibility.

How difficult is it to write DB front ends in Cocoa? Are there any classes, tools, modules available to make the job easier? And, is there any possibility of cross-compiling Cocoa apps to run on Windows?

When OS X Beta 1st came out I did a short tutorial with the Project Builder; that's the only Cocoa experience I've had (though I learned C programming back in 1990).

I'd appreciate any guidance,
Thanks


-- 
Randy Perry
sysTame
Mac Consulting/Sales

http://www.systame.com/

----
I'm happy to report that Mac OS X includes the EnterpriseObjectsFramework (aka  EOF), a fantastic tool for developing database applications.  Also, your applications will be immediately portable to run on Windows, thanks to Apple's YellowBox toolkit!

Oh wait, that was just a beautiful dream that could have been true if not for Apple marketing decisions.  Sorry, you'll have to find some other solution.

----

Well, there are several options for DB access from Cocoa. You can use MySQL or PostgreSQL, both of which have open-source Obj-C interfaces, or you can use OpenBase or FrontBase, and for embedded solutions there's MObjectiveSQL and SQLDatabase, or Valentina (C++ or C API now, but I'm working on a Cocoa/Obj-C one).

For Windows, Apple did discontinue YellowBox for Windows, but you might like to take a look at GNUStep, although you will probably find it's not a particularly viable solution for cross-compiling Cocoa GUI apps at the moment.

-- FinlayDobbie

