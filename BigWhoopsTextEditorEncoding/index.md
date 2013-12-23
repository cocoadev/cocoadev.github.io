---
layout: page
title: BigWhoopsTextEditorEncoding
---


I was trying to insert a subscript "2" into one of my .m files because I wanted to add an item to a popup button (specifically, a chemical compound thing). When I tried this, XCode asked me if I wanted to upgrade the encoding of the text editor to Unicode to allow this. I said okay. Then I tried building my project, and every single line produces an error or warning!

Any ideas? I've tried going into Preferences and messing with the Text Editor Line Encoding, but to no avail.

----
Remove the offending character and change your file's encoding back to something more normal, like UTF-8. Then get your symbol by putting it in an external file, or using     [NSString stringWithCharacters:length:] or something like that.

----
UTF-8 isn't normal enough.  It's safer to stick to ascii in source.

If you were trying to embed the non-ascii charater in a literal @"" or CFSTR string, that isn't supported anyway.  See UniCode for much discussion. 

----
UTF-8 is identical to ASCII for every character below 255.  That's why we use it instead of UTF-16.

----
Below 128, you mean. ASCII only defines values from 0-127, and above 127 UTF-8 starts using multiple bytes per character.

----
Right.  Whoops.  :)

