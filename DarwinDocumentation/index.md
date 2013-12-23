---
layout: page
title: DarwinDocumentation
---

There are some kernel functions which could be useful in normal Cocoa applications. But the API seems to be completely undocumented? not even the headers in /usr/include/mach/ carries any interesting comments, and using Google to search for some of the functions turns up < 5 hits...

Is the Darwin API undocumented? is there perhaps some documentation for the Mach or BSD APIs which can be used instead?

----

**man**, **whatis**, or **apropos** from the terminal. Or, if you prefer **Help / Open man page** in Xcode or Project Builder.

Also, in Xcode and/or on the Apple developer site, there is a section for Darwin.

In addition,

http://www.opensource.apple.com/projects/documentation/

and

http://opendarwin.org/documentation/

And, last but not least, there is the Darwin source code.

----

Thanks, but none of this documentation seems to document the things in /usr/include/mach/ -- for example try to find some documentation on the functions in semaphore.h.

Which would be: http://developer.apple.com/documentation/Darwin/Conceptual/KernelProgramming/synchronization/index.html but this page only mentions some of the functions, and say the documentation is in the header (which it isn't).

----

http://www.osxfaq.com/man/2/sem_open.ws --zootbobbalu 

also try 
    
man sem_open
man sem_clt
man semget
man semop
man sem_close
man sem_post
man sem_trywait
man sem_unlink
man sem_wait
man sem_umask



I don't remember if I found man pages for the vm_* functions I've been using, but     grep -r *whatever* /usr/include/mach is very much your friend.

----

I should have written mach/semaphore.h above -- the functions you list are not the ones in mach/semaphore.h (but a POSIX extension it would seem).

I don't know if POSIX does offer the same, as I e.g. have no idea what semaphore_wait_signal() is all about...

My actual problem is that I wish to wait for both a normal signal (normally done with sigwait()) and a semaphore of some sort, I do not think the function above does the job -- but would prefer the documentation nonetheless...

----

http://developer.apple.com/documentation/Darwin/Conceptual/KernelProgramming/synchronization/chapter_15_section_2.html

*Which, as stated above, does not document the functions � it only lists **some** of them, and state the documentation is in the headers.*

----

For the IPC functions you mentioned, It can be found on the sites of the links I gave you...

http://www.opensource.apple.com/darwinsource/10.3.1/xnu-517/osfmk/man/

http://cvs.opendarwin.org/index.cgi/~checkout~/src/xnu/osfmk/man/index.html?rev=1.1.1.1&content-type=text/plain

*Thanks a lot! I don't know which path you took with the first links you gave to arrive at the above, cause this is what I have been searching for in quite some time � unfortunately though, the documentation seems to be incomplete as there is e.g. no mention of:*

*semaphore_timedwait
*semaphore_wait_signal
*semaphore_timedwait_signal
*semaphore_operator


----

Sorry, I guess you can't have everything. ;-) File a bug report with Apple, as these seem to be extensions added to mach by NeXT or Apple. I was unable to find any reference to these functions outside of Apple that was not related to Darwin. As a note, the sem_ functions noted by zootbobbalu are the IPC primitive functions for SMP suport in BSD. While I have not checked myself, I would suspect (and this is easily verifiable in the Darwin source) that these boil down to wrappers/extensions around the mach primitives.

