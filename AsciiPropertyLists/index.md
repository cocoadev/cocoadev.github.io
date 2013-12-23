---
layout: page
title: AsciiPropertyLists
---

NeXTSTEP, OpenStep, and Rhapsody used a formatted ASCII text file as its PropertyListFormat. Mac OS X still supports this format for some things, although the preferred file format is XmlPropertyLists. The format looks like this: 

    
{
    food = pizza; 
    restaurants = (
        pizzahut,
        dominoes
    );
}


This format is still (currently?) used when printing datastructures to the user. You'll see this when inspecting ObjC objects in 'gdb', inspecting preferences with the 'defaults' command, and elsewhere.

You can read AsciiPropertyLists using Foundation objects' -initWithContentsOfFile method. Writing these property lists is a little harder. You must first get the ASCII representation of an object by sending it a -description method, then write the resulting string to file with -writeToFile:atomically:

*It should be harder - it's a deprecated format and nobody should be writing it anymore*

**I would also strongly advice against having programs write out AsciiPropertyLists using the above method, as there is no guarantee that     description outputs in that format. However, I do often use the format myself and also for programs i ship, since it's much easier to read/edit (by a human).**

See also PropertyList.

----

Is there any source code available for reading/writing these property lists?  I'd like to be able to use them outside of a Mac environment. 
-KenAspeslagh

*NSDictionary will read these just fine!* -- He said outside a Mac enviroment. CF and GNUStep run on Windows and other flavours of UNIX etc.

----

For reading, check out CoreFoundation. For writing (and indeed reading), take a look GNUStep. 

---- 

Reading and writing are both supported by CFLite, which Apple ships under the APSL license.  In fact, Apple even has an article specifically about getting property lists going on other platforms: http://developer.apple.com/opensource/cflite.html

Basically, nearly all of the code for NSDictionary, NSArray, NSString and friends is really in CoreFoundation, and most of CoreFoundation is open source.

