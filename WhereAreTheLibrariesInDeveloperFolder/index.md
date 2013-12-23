---
layout: page
title: WhereAreTheLibrariesInDeveloperFolder
---

If I include a library such as stdio.h, the C program compiles correctly.  Where is this library file stored on my hard drive?  When I search for it, I do not see it.  I also can't find math.h either.  Where are these files stored?

----

are you looking for the header of the binary?

What do you mean by the header of the binary?

----

stdio.h is at /usr/include/stdio.h

math.h is at /usr/include/architecture/ppc/math.h

the lib files themselves are under various /usr/lib subdirectories (/usr/lib/libc.dylib and /usr/lib/system/libmathCommon.dylib)

----

Is there a header file called conio.h?  If so where do I find it.  If not, how do I install it?

----

conio.h is a DOS thing. you should use curses under *nix.

[http://lists.debian.org/debian-user/2002/debian-user-200204/msg03137.html]

[http://www.phim.unibe.ch/comp_doc/c_manual/C/FUNCTIONS/funcref.htm#NONANSI]

Thank you for the help!
