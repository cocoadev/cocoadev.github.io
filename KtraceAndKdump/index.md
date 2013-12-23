---
layout: page
title: KtraceAndKdump
---

Hi folks,
does anyone know how to use use ktrace/kdump combination to react to some system calls? For example, I want to be informed if some program creates a directory on the disk...- BobC

----

You don't want to use ktrace and friends for this.  ktrace produces very voluminous output in an undocumented format, so you'd need to have kdump process that for you, which will end up being a CPU and disk intensive task.  Take a look at kqueue : http://people.freebsd.org/~jlemon/papers/kqueue.pdf, and check cocoa.mamasam.com for some postings related to kqueue.

For those wondering, ktrace and kdump are a pair of utilities that show all of the system calls that a program makes.  For instance, when dealing with files ktrace/kdump will show you the name of the file being opened, the file descriptor number (or else the error number if not returned), and show you the data read from and written to the file.  This is very handy for debugging a lot of problems (especially things like "it's not finding XYZ library", you can use ktrace/kdump to see where the program is looking.  You can check the man pages for usage, and BookCoreMacOSXandUnix discusses these utilities too.
----
See: http://developer.apple.com/samplecode/FileNotification/FileNotification.html which uses the kqueue method.

