---
layout: page
title: CoreDumps
---



To enable core dumps from Aqua applications, set     COREDUMPS=-YES- in /etc/hostconfig. Core files are created in /cores. They don't get cleaned up automatically on startup, so be careful that you don't collect too many of them.

Enabling core dumps from command-line applications depends on what shell you're using. For bash, use
    ulimit -c unlimited
For tcsh, use
     limit coredumpsize 64m
(These values are examples; see the man page for your shell for more details.)

----

You can manually dump a program's core with the infamous "kill" command:
    [localhost:~] mtrent% ps axw | grep tail
 2727  p5  S+     0:00.00 tail -f /tmp/log
 2729 std  R+     0:00.00 grep tail
[localhost:~] mtrent% kill -6 2727


You can use "otool" find out what application was responsible for a specific core file:
    [localhost:/cores] mtrent% otool -c core.2727
core.2727:
Argument strings on the stack at: 0xc0000000
        /usr/bin/tail
[ ... lots of shell variables ]


You can examine a core file with GDB to learn more about the crash. Note that your binary needs symbols (or you need to be able to link them in from a separate file) in order for the backtrace to make sense.
    [localhost:/cores] mtrent% gdb -q /usr/bin/tail core.2727
Reading symbols for shared libraries .. done

warning: core file may not match specified executable file.
#0  0x700252f8 in select ()
(gdb) bt
#0  0x700252f8 in select ()
#1  0x00002024 in ?? ()
#2  0x00003460 in ?? ()
#3  0x00001bc4 in ?? ()
#4  0x000019f4 in ?? ()
(gdb) 

----

You can instruct your program to dump core in certain error situations with the following function call: 
    #include <stdlib.h>

void abort(void) 

For example:
    if (ptr == NULL)
	abort();

This is good for narrowing down programs during the development cycle; though these references should be removed before shipping.

The abort system call simply sends the abort signal via the 'kill' function. When stepping through your program in GDB, GDB will break when your program receives the abort signal. This way you can debug a live system, which is often easier than scrounging through a core file.

----

By default core dumping is turned off (since it's so slow.  Probably having something to do with HFS+ not supporting files with holes in them)  You can enable core dumping programatically by using setrlimit
    
#include <sys/types.h>
#include <sys/resource.h>

void enableCoreDumps ()
{
    struct rlimit rl;

    rl.rlim_cur = RLIM_INFINITY;
    rl.rlim_max = RLIM_INFINITY;

    if (setrlimit (RLIMIT_CORE, &rl) == -1) {
        fprintf (stderr, "error in setrlimit for RLIMIT_CORE: %d (%s)\n",
                 errno, strerror(errno));
    }

} // enableCoreDumps


This is handy if you want to let end-users turn on core dumping (say through a checkbox in the UI) and not have them mucking around in /etc or the terminal) ++MarkDalrymple

