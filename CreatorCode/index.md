---
layout: page
title: CreatorCode
---



A creator code is a string of four bytes that uniquely identifies an application. It traditionally represented four MacRoman characters (for example, QuickTime Player's code was TVOD), but can be any four bytes. For instance, GrowlTunes' creator is     G\xE2\x99\xAB. The last three bytes represent a beamed eighth note in UTF8, so it appears as two characters when it's edited in XCode. Creator codes are the traditional MacOS way of identifying application programs, but they have been partially supplanted by Bundle Identifiers in MacOSX. http://goo.gl/OeSCu

Creators can be edited in XCode under     Target Info -> Properties.

(It is necessary to use an xCode native target to have this option.)

If you're going to be releasing this app into the wild, you should register your code at
[http://developer.apple.com/datatype/creatorcode.php]
to ensure it's unique.

vThe history is that, in earlier versions of System and Finder, all files had a creator code and a file type � each was four MacRoman characters long. They were used to identify which files were created by which applications, and which applications could open which files, and what happened by default when you double-clicked on a document file � so, for instance, a JFIF file might have been created by Photoshop, but openable by GIFConverter or Freehand or Illustrator (because the 'BNDL' resource in all those apps would identify the app as capable of opening files of type 'JPEG'). There were standard procedures about which were used for development and which were reserved by Apple, and all others were registered with Apple, free for the taking. I don't recall much in the way of specifics, though � the last time I dealt with any of this was when System 7 was new and shiny. Releasing an application into the wild with a creator code that wasn't unique was a very bad thing to do, and I'm not sure exactly what happened to people who did it � probably nothing beyond their apps getting a reputation for not behaving. In OSX there are other ways to handle creating codes that need to be unique, but because of backwards compatibility the four-character type and creator codes are still around. Not all applications use them, anymore.

Apple says: "Creator codes containing all lowercase letters are reserved for use by Apple. Your creator code must contain at least one upper case letter." However, they're silent on how � and � are considered as "letters."

----

**Discussion**

Four characters may seem not enough to ensure uniqueness. Take a hint from AmbrosiaSW, use uncommon characters, like � or �. (If you can't see either of those, try switching to a Unicode-compatible browser). ASCII Extended is certainly supported, and OS X might allow Unicode characters. This really helps when trying to avoid collisions.

----

That's why you register it with Apple.  You as a programmer can't guarantee uniqueness across the possible universe of creator codes at all, with any length of string -- there's no way of knowing someone else won't have the same brilliant idea to obfuscate the creator code -- but Apple guarantees that they'll only let one person register a particular creator code.  It's actually pretty simple and fast and free, and something you really really need to do if you intend to distribute your application at all.

(The history is that, in earlier versions of System and Finder, all files had a creator code and a file type - each was four characters long.  They were used to identify which files were created by which applications, and which applications could open which files, and what happened by default when you double-clicked on a document file -- so, for instance, a JPG file might have been created by Photoshop, but openable by GIFConverter or Freehand or Illustrator.  There were standard procedures about which were used for development and which were reserved by Apple, and all others were registered with Apple, free for the taking.  I don't recall much in the way of specifics, though - the last time I dealt with any of this was when System 7 was new and shiny.  Releasing an application into the wild with a creator code that wasn't unique was a very bad thing to do, and I'm not sure exactly what happened to people who did it -- probably nothing beyond their apps getting a reputation for not behaving.  In OSX there are other ways to handle creating codes that need to be unique, but because of backwards compatibility the four-character type and creator codes are still around.)

----

Apple sez: *"Creator codes containing all lowercase letters are reserved for use by Apple. Your creator code must contain at least one upper case letter."* They're silent on how � and � are considered as 'letters'

----

My understanding was that cc's were 4 byte values which were displayed as MacRoman characters for programmer/user convenience, so any MacRoman 1-byte character can be used in the cc, including less-used, > 128 characters. -- l0ne

----

OSTypes (including creator codes) are four-byte, not four-character. MacRoman is the general form of display - except that Xcode actually uses UTF-8! GrowlTunes (which comes with Growl - http://growl.info and WhatIsGrowl) has a creator code of 'G\xE2\x99\xAB'. the last three bytes are a beamed eighth note in UTF-8. it displays as those two characters (G and the note) in Xcode. (that creator code was my idea, btw. :)

and no, OSType is not related to CFBundleIdentifier.

----

But it is related to CFBundleSignature... is there a simple way to convert the CFBundleSignature (NSString/CFStringRef) into a 4-byte OSType?

