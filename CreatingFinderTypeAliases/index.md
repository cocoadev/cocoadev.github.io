---
layout: page
title: CreatingFinderTypeAliases
---

Hello,

How could I create Finder-like aliases? I tried to read Carbon Alias Manager ,but I can't figure out how to create aliases. I also tried to use NDAlias which uses same Carbon Alias Manager but still no luck. :(

----

I assume you mean Finder alias files? i.e., not alias records themselves, which are just handles / blobs of data. A Finder alias is an on-disk container for an alias record.

There's an ancient Apple technote (last updated 1993) which says not to do so, 'cause it's private.
 http://developer.apple.com/technotes/tb/tb_535.html

Of course, there's a newer Apple technote (last updated 1999) which provides source code to do so, at least in the context of creating a bundle.
 http://developer.apple.com/technotes/tn/tn1188.html

So it seems to be a mixed message. :-)

The basic summary is that the Finder alias is a resource file, with one 'alis' resource inside. The sample Apple provides makes a relative alias, but you can also create a non-relative (absolute) alias by passing NULL for the "fromFile" parameter of NewAlias. There are two important bits of metadata that you must set on the file:

First, set the "kIsAlias" bit in the flags (FileInfo.finderFlags or FInfo.fdFlags). When you set it, you must also clear the "kHasBeenInited" bit, which tells the Finder that you've been poking around with the file, so that it can fix it up if needed.

Second, set the file type of the alias (FileInfo.fileType or FInfo.fdType). If you look in CarbonCore/Finder.h, you'll see a bunch of special alias file types. These are used by the Finder to provide special icons for certain kinds of aliases. You should make an attempt to set the type correctly, but the list is rather large so it might not be simple to do so for all possible alias targets. If you're just creating one particular type of alias it's easier. (The Apple sample code above checked for the application type, 'APPL', and set the file type to kApplicationAliasType / 'adrp'.)

 -- DrewThaler

I had great success with NathanDay's NDAlias and it's new category that allows writing real aliasses to disk.

-- MartinHaecker

