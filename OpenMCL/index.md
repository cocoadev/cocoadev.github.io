---
layout: page
title: OpenMCL
---




OpenMCL is a free, open source version of Macintosh Common Lisp. It is an implementation of ANSI Common Lisp [http://www.lisp.org/].


*Home: http://openmcl.clozure.com/
*Download: http://openmcl.clozure.com/Distributions
*Mailing Lists: http://openmcl.clozure.com/mailing-lists
*Documentation: http://openmcl.clozure.com/Doc
*Support: http://www.clozure.com


On MacOS X OpenMCL has an experimental **Cocoa** interface:
http://lemonodor.com/archives/images/openmcl-ide-rainer.png

OpenMCL has a fast incremental compiler and compiles directly to PowerPC machine code. OpenMCL is a relatively compact Common Lisp implementation. OpenMCL is written in a mix of some C, some Assembler and Common Lisp.
The whole OpenMCL implementation can be recompiled in a few minutes.
OpenMCL has an inline PowerPC assembler with AltiVec support.
External Shared Libraries can be used.

OpenMCL runs on LinuxPPC and MacOS X. The MacOS X version is a native implementation.

Currently the typical development environment for OpenMCL is Emacs + Slime.

Like any implementation of ANSI Common Lisp, OpenMCL supports CLOS, the Common Lisp Object System. CLOS provides the Lisp developer with a rich facility for object-oriented programming (objects, classes, slots, generic functions, methods, meta classes, multiple inheritance, multi-dispatch, ...).  It also includes fully native threads with support for multiple CPUs.

OpenMCL is under active development by Gary Byers of Clozure Associates [http://www.clozure.com]. Clozure also offers commercial support contracts and consulting.  Next major feature (as of September 2005) under development is support for 64 bit PowerPC under both Linux and MacOS X.

Since OpenMCL is based on Macintosh Common Lisp, the core Common Lisp, even in the version OpenMCL 0.13.2, is very mature.

<code>
    
[foo:~] foouser% openmcl
Welcome to OpenMCL Version (Beta: Darwin) 0.13.2!
? *features*
(:PRIMARY-CLASSES :CCL :CCL-2 :CCL-3 :CCL-4 :CORAL :COMMON-LISP
:MCL :OPENMCL :ANSI-CL :PROCESSES :INTERFACES-2 :POWERPC :PPC-TARGET
:PPC-CLOS :DARWINPPC-TARGET :DARWINPPC-HOST :DARWIN :POWEROPEN-TARGET)

? (room)
There are at least 69,099,000 bytes of available RAM.

                  Total Size             Free                 Used
Lisp Heap:    70694536 (69038K)     69099000 (67479K)      1595536 (1558K)
Stacks:        5192224 (5071K)      5189836 (5068K)         2388 (2K)
Static:        2181584 (2130K)            0 (0K)         2181584 (2130K)
922.469 MB reserved for heap expansion.

? (list-all-packages)
(#<Package "ILISP"> #<Package "OPENMCL-SOCKET"> #<Package "INSPECTOR">
#<Package "OS"> #<Package "ARCH"> #<Package "ANSI-LOOP"> #<Package "PPC">
#<Package "COMMON-LISP-USER"> #<Package "SETF"> #<Package "CCL">
#<Package "COMMON-LISP"> #<Package "KEYWORD">)

? (require 'cocoa)
;Loading #P"/Volumes/OSX/OpenMCL-0.13.2/ccl/examples/cocoa.lisp"...
;Loading #P"/Volumes/OSX/OpenMCL-0.13.2/ccl/examples/apple-objc.lisp"...
;Loading #P"/Volumes/OSX/OpenMCL-0.13.2/ccl/examples/cocoa-window.lisp"...
;Loading #P"/Volumes/OSX/OpenMCL-0.13.2/ccl/examples/cocoa-listener.lisp"...
;Loading #P"/Volumes/OSX/OpenMCL-0.13.2/ccl/examples/cocoa-editor.lisp"...
;Loading #P"/Volumes/OSX/OpenMCL-0.13.2/ccl/library/pty.lisp"...
"COCOA"

? *all-processes*
(#<PROCESS Listener(3) [input-wait] #x55072B6>
 #<PROCESS Cocoa Event Loop(2) [Cocoa Event Poll] #x550680E>
 #<PROCESS external-process-watchdog(1) [Arrested] #x517F85E>
 #<PROCESS Initial(0) [Running] #x5071126>)

? (run-program "df" '("-k") :output t)                                    
Filesystem              1K-blocks     Used    Avail Capacity  Mounted on
/dev/disk1s12             6692920  6114720   511272    92%    /
devfs                         100      100        0   100%    /dev
fdesc                           1        1        0   100%    /dev
<volfs>                       512      512        0   100%    /.vol
/dev/disk1s9              6692920  6192288   500632    92%    /Volumes/MacintoshHD
/dev/disk1s10             6692920  3360680  3332240    50%    /Volumes/data01
/dev/disk1s11             6692920  6004396   688524    89%    /Volumes/data02
/dev/disk0s9             20969604 14148720  6820884    67%    /Volumes/OSX
/dev/disk0s10            96239648 81361472 14878180    84%    /Volumes/data
automount -fstab [484]          0        0        0   100%    /Network/Servers
automount -static [484]         0        0        0   100%    /automount
automount -fstab [565]          0        0        0   100%    /Network/Servers
automount -static [565]         0        0        0   100%    /automount
#<EXTERNAL-PROCESS [1564] (EXITED : 0) #x518A5DE>

</code>

See also:  AllegroCL, LispWorks

