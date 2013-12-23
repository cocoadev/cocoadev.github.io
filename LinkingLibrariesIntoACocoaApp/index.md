---
layout: page
title: LinkingLibrariesIntoACocoaApp
---



Not even sure this title is exactly the way to describe it, but here's my question

I've been working on a little cocoa app, a GUI for SQLite, and I'm wondering if it's at all possible to link the SQLite library into the app and have it completely linked in I guess?
Basically so a person running the app doesn't need the SQLite library installed in /usr/local/lib or wherever it's installed in for me

Any ideas?    --- JeremyK

*I think I've seen this done so that the library is stuck in the app's package, in Frameworks, so it's distributed with the app... not that I'd know how to do that. Or maybe there's a way to compile it in, I dunno.*

----

If you have an SQLite framework, it's easy enoug to make a copy files build phase to copy it into YourApp.app/Contents/Frameworks -- but if what you're dealing with is something like "sqlite.dylib" or .so -- then I don't know. I had a similar situation, and wasn't able to solve it without linkig the binary directly into my app's executable -- anyway it seems the linker behaves differently for dylibs vs frameworks. I know *nothing* about SQLite, so take this with a grain of salt, but it's possible you can rebuild it to make a framwork, like you can with Python by specifying a ./configure option. But then, there's still mojo that must be done to make the framework embeddable, and I'm not certain that can be done after the framework's built. See EmbeddingFrameworksInApplications
--ShamylZakariya

----

I spent a little bit of time a while ago putting SQLite into a framework- you can download it from here:  http://flyingmeat.com/x/FMSqlite.sit
Feel free to use it as you wish-  it also uses the SQLDatabase classes.

-- GusMueller

----
Sweet, a framework is what i need, because I knew you can embed those. thanks a million, i'll look into doing that :) now on to the NSDocumentProblems page to fix that little issue, then onto coding! --JeremyK

----
I'm a little confused on how to build the framework since the framework looks for sqlibs/.libs/libsqlite.a  but there's only a sqlibs/libsqlite.la
and couldn't find anything obvious in the makefiles that make .libs/libsqlite.a ... :-/
anybody?

--JeremyK

----

Please, please, can somebody help with this thing? I'm trying to embed PostgreSQL client library in my software. I built PostgreSQL with Fink and I can get it to link nicely with the software. And it runs smoothly as long as the PostgreSQL dylib is where it was built, inside Fink's directories. How could I embed the library inside my application? What do I need to set to make the runtime system look for the libraries inside my application bundle?  --Petteri

----

Add the static library (ends with .a instead of .so or .dylib) to your project and build it. Your project will no longer be linking against a dynamic library, so there's no need for a library to be linked in at runtime. For PostgreSQL, you need to add the libpq.a file to your project (remove any libpq.dylib or libpq.so files from the project).

If Fink didn't install a static libpq.a library, go download the PostgreSQL source code and compile it yourself. It's just a simple ./configure && make && sudo make install. Be sure to install GNU readline first.

Chapter 5 of BookCoreMacOSXandUnix from BigNerdRanch has all sorts of wonderful information about using libraries.

----

Great! Thanks a million! I'm running into Kerberos dependency problems, but I think I can sort them out by compiling PostgreSQL myself. I had tried the .a version, but I must've had the dylib version in the project at the same time. Again, thanks very much. --Petteri

