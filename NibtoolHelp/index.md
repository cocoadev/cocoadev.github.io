---
layout: page
title: NibtoolHelp
---

I cannot make nibtool create a new .nib file after I have translated all the strings!
I'm getting the following error:

nibtool -d LibraryFinder.strings -w LibraryFinder.nib ../English.lproj/LibraryFinder.nib

nibtool[6417] CFLog (0): CFPropertyListCreateFromXMLData(): Old-style plist parser: missing semicolon in dictionary.
nibtool[6417] CFLog (0): CFPropertyListCreateFromXMLData(): The file name for this data might be (or it might not): LibraryFinder.strings
nibtool: could not open 'LibraryFinder.strings'.

The file has the correct format created with the same nibtool...

What am I doing wrong?

Thanks, FelipeBaytelman

----

Utterly wild guess but... maybe your LibraryFinder.strings file is missing a semicolon, like the log says it is?!

