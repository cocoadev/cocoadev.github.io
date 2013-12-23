---
layout: page
title: NSDataAndResourceForks
---



Hello all, I am trying to package an update for a CarbonCFM program, I need to use NSData to store the CFM application, the problem however is that NSData does not work with resource forks.

Does anyone know how I can preserve the fork (in objC) and then set it to a NSData object.

All of this will then be saved to a plist for use later.

Thanks alot!

----

There are several possibilities:

- Use Carbon functions to read the file fork into your NSData.

- Use a MacBinary, BinHex, or other package format which preserves resource forks. This has the added advantage of preserving other HFS file attributes as well, such as type and creator code, which will be necessary for your application to function.

- Do the secret resource-fork dance, where opening     /path/to/file/rsrc will let you read the file's resource fork using "normal" file reading functions.

Of these three, using MacBinary is probably the best approach. It's there, it works, and it will save all of the attributes you need.
----
    /path/to/file/rsrc is deprecated (and has been for some time, and may not be around much longer). Use     /path/to/file/..namedfork/rsrc instead

----

Can you explain that?  If I want to deal with the standard resource fork, what is the right value for "..namedfork"?

*What does your question mean? Everything after the "/path/to/file" part is literal.*

I seriously doubt you intend to type the string "namedfork" on the command line.  What I mean is, I do not know what are typical fork names.  In the old style, there was one data fork and one resource fork.  Fine.  What are the names of these forks if I want to refer to them by name?  For example, if I want to set the custom icon for a file, it's pretty much going to have to go in whatever fork the Finder expects.  What is the name of that fork?

----

You seem to be somewhat confused. "namedfork" is indeed literal, as is "rsrc". Here is my getting the size of my Classic System file's resource fork in Terminal:
    
Hope:/Volumes/Eloquence/System Folder mikeash$ wc System/..namedfork/rsrc
   31435  290862 7946161 System/..namedfork/rsrc

A custom icon goes in the resource fork. The tricky part is that it goes into *a specific resource in the resource fork*. To do that, you have to use the Carbon Resource Manager, unless you want to write your own workalike. There is almost never a reason do read or write to the resource fork raw; this kind of archiving situation is one of the exceptions.

As far as I know, HFS+ only supports two forks, resource and data, and the same is true of Mac OS X. Does anybody know for sure if that's true in general? For example, can I access arbitrary forks this way if I have an NTFS volume mounted?

*See [http://developer.apple.com/technotes/tn/tn1150.html]. Support for named forks is there, but it's not fully defined yet.*

----

wow - so if I have a photoshop document with a custom icon and I read the contents of it to an NSData, then read the contents of /path/to/photoshopdocument/rsrc into another NSData, then create a filehandle to an empty file, and write both NSDatas to it I will have a copy of the original document, with custom icon intact?

EcumeDesJours

*No, you will still be missing HFS file attributes, such as type and creator code and special flags. I think custom icon is one of these flags, so the icon might not work. Since Photoshop is a rather old-school program, it probably relies on the type code, and the file might not work because of that either. You will get all of the data, but not all of the metadata.*

**Ecume, you might want to look at HowToSetACustomIconWithRez.  The line with SetFile is the incantation to inform the Finder of the custom icon.**

----

Thanks alot! Using mac binary worked! I'll post the code here for everyone else to use later today.

----

Here you go all (don't post in that page or you'll go over the wiki page limit): NSDataAndResourceForksCode

----

Anyone know if the ..namedfork/rsrc is available in 10.2 and 10.3, or if you have to use the deprecated /rsrc there? -- Kristofer
----
Yes, available in Jaguar, *videlicet*,
    
% uname -rv
6.6 Darwin Kernel Version 6.6: Thu May  1 21:48:54 PDT 2003; root:xnu/xnu-344.34.obj~1/RELEASE_PPC 
% ls -l File_with_resource_fork.webloc 
-rw-r--r--  1 user  unknown  0 Apr 27 07:42 File_with_resource_fork.webloc
% ls -l File_with_resource_fork.webloc/..namedfork/rsrc
-rw-r--r--  1 user  unknown  530 Apr 27 07:42 File_with_resource_fork.webloc/..namedfork/rsrc
% 

