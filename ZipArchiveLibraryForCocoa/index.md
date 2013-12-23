---
layout: page
title: ZipArchiveLibraryForCocoa
---

Does anyone know of a Zip class, library, framework, etc... for Cocoa? I want to be able to Zip files and folders with my program, and I am currently General/NSTask-ing to Unix to the built in Zip tool. The only problem is that when the files are decompressed, the Mac headers have been stripped, so Mac OS dosen't recognize the file!! (i.e. I zip an application and it stripps the "appl" from the file. When I unzip it, it is unusable.) The files need to be compatible with windows and Mac systems. I know that the stripping is not part of the Zip format, since utilities such as General/ZipIt seem to create zip files that unzip just fine.

--FD

----

I found this site:

http://www.artpol-software.com/index_zip.html

which has General/ZipArchive Libraries for C++. I heard mention of "Putting and General/ObjC wrapper" around a C++ library, but I don't pretend to have the first idea how that would be done.

--FD

----

It wouldn't be that difficult to do, but I think he may still have the same header stripping problem.  This may be a longshot, but in /System/Library/Core Services there is the General/BOMArchiveHelper which I *believe* handles the archiving done standard on Panther now.  You could try to call that.

- General/FranciscoTolmasky

*It really sucks that it's done this way. There should be a General/ZipArchiveFramework. Or even just a General/NSFileManager method.     createArchiveForPath:(General/NSString *)sourcePath atPath:(General/NSString *)destinationPath or something.*

----
I noticed under an older entry that the ditto utility will zip files/directories under Panther. 

Indeed, yes it will -- General/MikeTrent

----

See also: General/ZipArchive General/ZipFileFormat

----

I've got some somewhat crude code for dealing with ZIP archives, it's implemented as a classcluster, so it shouldn't be hard to make it work for other archivers to.
If anyone's interested you can mail me att S-P@home<NOSPAM>.se

----

There used to be a Zip.framework in Public Beta, but it was removed when 10.0 was released. Anyway, zlib is included standard with OS X nowadays. You can use that. As for making it preserve resource forks, try zipping a file with Finder and take a look at the directory structure it creates in the zipfile.

----

I made a quick framework off of minizip ( http://www.winimage.com/zLibDll/minizip.html ).

It is available from svn here: http://svn.deck.dk/ziplib (username: anonymous, empty password).

Hopefully it will let you escape from the evils of calling command line utilities. The "branches/carbon-only" version is basically the minizip zip and unzip functions wrapped in a framework, while the trunk version includes an objc wrapper (found in zip/General/ObjZip.h) which IMHO is quite a bit easier (but less versatile) than using the c functions directly.

----

Just ran across this while researching this very topic:
http://code.google.com/p/zip-framework/

----

I wrote a C wrapper for zlib + minizip that preserves Mac OS X resource forks / extended attributes like Apple's Archive Utility, here it is: http://github.com/dchest/osxzip/

Basically, there are just two functions:

    
int compress_path(const char *source, const char *zipfile, int level);
int decompress_path(const char *zipfile, const char *destination);


For example, compress_path([sourcePath UTF8String], [zipFile UTF8String], 9).

It should be fast with lots of files.

-General/DmitryChestnykh

----

Z<nowiki/>ipKit, a new Objective-C Zip framework that supports Archive Utility-style resource forks (optional), zip64 extensions, clean interruption. It can be built as a Mac OS X framework or static library, or as an iPhone static library, and comes with demo app projects. More info and BSD licensed-source code is available at http://bitbucket.org/kolpanic/zipkit/

----

http://code.google.com/p/ziparchive/

this is a objective-c class that does what you're looking for.

i haven't had an opportunity to use it yet, though.

--lowell
