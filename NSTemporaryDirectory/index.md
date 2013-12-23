---
layout: page
title: NSTemporaryDirectory
---

Returns the temporary directory on the user�s system.

Also see UniqueFileName

----

From Apple:

To obtain the path to the default location to store temporary files (stored in the $TMPDIR environmental variable), you can use the NSTemporaryDirectory function, which calls theFSFindFolder and FSRefMakePath functions for you. Note that NSTemporaryDirectory can return /tmp under certain circumstances such as if you link on a pre-Mac OS X v10.3 development target. Therefore, if you're using NSTemporaryDirectory, you either have to be sure that using /tmp is suitable for your operation or, if not, you should consider that an error case and create a more secure temporary directory if that happens.

----

Despite how it is documented, it does not merely return     @"/tmp" in a fancy manner. Instead, it (currently, as of 10.4.6) appears to return a subdirectory of     /private/var/tmp. On my computer running 10.4.6, it gives me     /private/var/tmp/folders.502/TemporaryItems. Using a user-specific subdirectory in this manner increases privacy and helps avoid a certain class of security hole that can happen if a program writes to a known file name directly in     /tmp. NSTemporaryDirectory will presumably hide all of these details from you by creating the directory when required, although this is not explicitly documented.

Note that the documentation claims that this function can return     nil. If you have code which uses it, that code should be prepared for a     nil return value, either by failing gracefully or substituting a known good path.

----

**Regarding use of tmpnam() or tempnam()** (from 'man tmpnam')

The tmpnam() and tempnam() functions are susceptible to a race condition occurring between the selection of the file name and the creation of the file, which allows malicious users to potentially overwrite arbitrary files in the system, depending on the level of privilege of the running program. Additionally, there is no means by which file permissions may be specified. **It is strongly suggested that mkstemp(3) be used in place of these functions.**

Note that the directory returned by NSTemporaryDirectory is currently private which closes this security hole. However it is still advisable to avoid these functions and the techniques they follow when possible, since it's possible to have buggy behavior even without the security hole.

----

Question: Do I need to clean up my temporary files, or does the system handle it for me?

You are responsible for deleting any temporary files that you created. The system will clean them up at startup, but that could be a very long time away.

----
From: http://lists.apple.com/archives/cocoa-dev/2009/Jan/msg01062.html

Do not just use NSTemporaryDirectory if you need to perform an atomic
save operation (write new data to location X and then overwrite old
data at location Y by moving X -> Y).  If will not work if the
destination is on a different volume from the source.

In this case you must use FSFindFolder with the volume reference
number of the volume on which you wish to save your data.  Read
Folders.h for more information.

----

Here's a useful page in that circumstance: GettingTemporaryFolderOnSpecificVolume

