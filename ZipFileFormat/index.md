---
layout: page
title: ZipFileFormat
---

.ZIP File Format Specification

http://www.pkware.com/products/enterprise/white_papers/appnote.html

See Also: General/PkWare

----

OS X has a a GZIP library built-in, named *libz*. It uses the same algorithm as General/PkWare's ZIP, but I don't know if the header is the same format. *libz* can be used by simply including the proper header (*zlib.h*) and calling the right functions, documented at http://www.gzip.org/zlib/manual.html. See General/NSDataCategory for an **inflate** category method on General/NSData.

Note that GZIP data doesn't provide a decent way for you to find out how long it is, that I can see. There is an General/NSData category out there at http://www.withay.com/macosx/nsdata_compress.html and it knows the length, but that's because it uses a separate header of its own. It cannot be used to read third-party General/GZIPped data.

----

The libz library (zlib.h in /usr/include) only supports the "deflate" compression method and works only on compressed data streams. The ZIP file format not only contains the compressed data (which it allows for many more compression schemes than deflate) but also manages multiple streams within the same archive. Unfortunately, libz does not handle the later for you.

----

Also, in Panther, the "ditto" command will write files and folders to General/PKZip file archives. For what that's worth ... -- General/MikeTrent

----
Question: (4/7/2005) What is the easiest way of getting ZIP code working within your app, on the Mac? zlib is not zip, so we don't get a ZIP library. Is the best way to use the unzip.c/zip.c files (in the contrib directory of their official source download) from zlib.org?

*Easiest method is to use General/NSTask! Most efficient? Probably not.*

Totally off-topic, but it would be better date entries in a less ambiguous way, such as 2005-07-04. Writing 4/7/2005, it's not clear whether that's April 7th or July 4th.

*Seeing as the wiki timestamps everything (below the page title), that's not really necessary if you really need to know. ;-)*

I assume you mean use the zip/unzip command line tools? I have no idea about on which version of General/MacOSX they are supposed to be installed on, or if they are installed regardless of what installation options you choose (IE, not choosing the BSD subsystem on an OSX install). I can't release an app which relies on tools which I don't know if they are installed or not! *You asked for the easiest, and I provided. No fussing!*

Well, you could *find out* if they're installed by default or not. Use lsbom in /Library/Receipts.
