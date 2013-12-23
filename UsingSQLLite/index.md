---
layout: page
title: UsingSQLLite
---

I am trying to use the General/SQLLite wrapper classes from Blackhole Media with Cocoa as I need a embedded database for my application. However I am having trouble in installing it. I did download the SQL Lite library from http://www.hwaci.com/sw/sqlite/download.html but I don't a have a clue how to install it. Can someone please give me detailed step by step instructions on doing that.

Thanks Saad R. Abbasi

----

Someone put together a binary package for the most recent version of General/SQLite and listed it on General/MacUpdate (http://www.macupdate.com/info.php/id/14409 ).  You can just download that and add the libsqlite.a and sqlite.h files to your project.  -- Bo

----

I have downloaded and installed everthing from General/MacUpdate but I still can't get the General/SQLite Demo from Blackhole Media to compile, it says 'can't locate file for: -lsqlite'. - Saad R. Abbasi

----

OK I'm going to assume you are starting from "square one".


*download General/SQLite http://www.hwaci.com/sw/sqlite/

*tar xzf sqlite.tar.gz
*mkdir bld
*cd bld
*../sqlite/configure
*make



Now you have a build of sqlite in the directory bld. The "bld" directory will contain a subdirectory named ".libs". This is where the dynamic libary for sqlite resides. You can create a link for this shared library in /usr/local/lib. (e.g.     sudo ln "path to libsqlite.0.dylib" /usr/local/lib/libsqlite.0.dylib). "path to libsqlite.0.dylib" is the path to file that is in     bld/.libs named "libsqlite.0.dylib" If someone knows of the better way to do this please speak up, because I would like to know of a cleaner way to deploy sqlite!!

Now all you have to do is drag libsqlite.dylib from the "bld/.libs" directory into your project and build away!! --zootbobbalu

----

If you add a     sudo make install at the end of the build process it'll copy the libraries to /usr/local/lib and the program to /usr/local/bin.  Also, it's a good idea to enable UTF-8 support by passing a parameter to your configure like so:     ../sqlite/configure --enable-utf8  -- Bo

PS Saad, did you actually add the libsqlite.a and sqlite.h files from the distribution to the demo project?  The error you're reporting seems to suggest that it couldn't find the libsqlite.a file.

----

Hey Bo, 

Thanks for the input, your comment helps, and your advice also helps someone get their development project going. What are your thoughts on how one would go about creating a copy phase for deployment? -�zootbobbalu

----

You mean like the copy files build phase in General/XCode or copying stuff when installing on the user's computer?  -- Bo

----

Yes, what would be the best way to install this dynamic library on a users system?

----

I'd say forget about it and just link against the static library.  If that's not an option, then second choice would be to use General/XCode's Copy Files build phase to place the dynamic library inside the application bundle.  Unfortunately, you need admin privileges to copy to /usr/local, so you're gonna be stuck either using General/PackageMaker, a third-party installer or handling the authentication yourself (which is a royal PITA) if you decide to go that route.  -- Bo

----

That's what I didn't want to hear. Too bad Apple hasn't come up with an easier way to copy/link/install shared libraries. If you copy the library into a bundle, what configurations do you need to do to get this to work? --zoot

----

To tell you the truth, I haven't tried this yet under General/XCode.  Going from my experience with General/ProjectBuilder, there are basically 2 steps: adding the build phase that copies the library into your bundle, and telling your app to look for libraries in your app bundle.  To do the first, you add a Copy Files build phase to your target, set the Path to the dylib's path and the destination to 'Executables'.  For the second, find the 'Library Search Paths' build setting for your target and add '@executable_path' to the (comma-separated IIRC) list, which will be automagically expanded to your executable directory at runtime.  It's very similar to the procedure for frameworks in General/EmbeddingFrameworksInApplications.  -- Bo

----

Hey Guys I got the database to work for that I thank you, now could you please direct me to a SQL Reference that General/SQLLite supports. - Saad R. Abbasi

----

There's a reference document on the General/SQLite site at http://www.hwaci.com/sw/sqlite/lang.html  -- Bo
