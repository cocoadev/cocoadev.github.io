---
layout: page
title: CleanFilePathWithOddCharacters
---



What is the proper way to take a string with any possible characters and making it into a valid filename?

For example, it might contain an asterisk or a ? or : or / or \ or ", etc... all these seem to cause NSFileManager to complain about being unable to create a file named that. How do I convert it to something it can always work with?

Right now I am just replacing occurances of those characters, but that is yucky!

----
yes i would like to know too, recently i've seen NSString fileSystemRepresentation method, but that return a const char* ... reconverting it back to an NSString seems strange to me, i think i didn't understand the main goal of this method :)
----

There is no universal way to do this, because the allowed characters is a function of the filesystem, not the operating system. For example, all of the characters you quoted except for / are allowed on an HFS+ volume, but not on a FAT32 or NTFS volume.

    -fileSystemRepresentation exists so that you can take an NSString representing a path, and get a     char * which is suitable for passing to C and POSIX functions, such as     popen() or     stat().

