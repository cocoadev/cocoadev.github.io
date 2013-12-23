---
layout: page
title: CoverArtIniTunes
---



So some of us recently saw the nifty new screen saver that combines all the cover art in your iTunes library. After having seen this, I have a few questions:


*1 - Where is the cover art stored? Inside the music file?
*2 - How can we retrieve this using Cocoa (in MP3 and AAC)?
*3 - What are the legal ramifications of, say, using this obviously copyrighted cover art on a web site (eg, the user 'publishes' their playlist)? Fair use?


Discuss ...

----

**Where / How is it stored?**

It is stored in an ID3 tag, amazon.com allows anyone to access their album art DB.

*What, exactly, is stored? A reference for external databases like the CDDB ID code?*

The Image is stored in the ID3 tag.

*Thanks! Next question ...*

----
**How can I retrieve the image data from an ID3 tag?**

?


Clutter does this - http://sprote.com/clutter/ It's open source.

*Thanks, I'll check out their approach.*

----

I happened across this thread and was very hopeful, since I'm looking for the same information.  I've checked out Clutter's method, and they seem to access the album data (unless I read the source incorrectly) through the use of an AppleScript.  I'm interested in getting the album art using just Cocoa (and whatever SDK's might be necessary).  Is anyone aware, programatically, how I can obtain this cover art that may already exist in a file?

Thanks!

----

You can programmatically call applescript from cocoa.  See NSAppleScript.

*Thanks, I know that is possible, but I was wondering if there is a way to do it that doesn't involve AppleScript.  Is it an ID3 tag I can use an API to get to?  Maybe a separate track I can use Quicktime to get to....?*

Nope, it's in the ID3v2 tag at the start of the file (ID3v1 doesn't support near as many fields). See: http://www.id3.org/ for the specifications; there are also some ID3 libraries around you could use.

*Check out http://drewfamily.homemail.com.au/Cocoa_-_ID3Tag_framework.html.  He seems to provide a Cocoa framework for accessing ID3v2 data.  This is all Cocoa.  id3lib (as mentioned above) is the standard in id3 libraries used.*

Great resource!  I'll check it out.

