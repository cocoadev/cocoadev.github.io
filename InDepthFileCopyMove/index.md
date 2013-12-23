---
layout: page
title: InDepthFileCopyMove
---

What i am trying to investigate and document are all the methods for copying and moving files on osx and all their implications.

Obviously this is all regarding the HFS/HFS+ file system and osx 10.4 and on.
 
At this point i am aware of two ways to do file system operations like copy/move on osx : 

* 1 shell/posix way (with cp or a console file manager like mc) 
* 2 finder/cocoa way (with finder or another file manager)
 

A number of questions arise from the undocumented fact the action of copying or moving a file is very different between 1 and 2.

Let us start with /dev/fsevents / FSEvents , file system changes made with method 1 seem to go unnoticed and the os is not aware anymore of moved files.
Is this correct and is there any way about this ?

Is the file system operation actually performed by the same C i/o component or system calls in both case 1 and 2 , or different ways ?
In case 2 all the difference from 1 are additional instructions during the file system operation that can be implemented for a shell/C program ?

Also a known fact is that a shortcut/symbolic link done with finder is different from one did with ln in the shell .
I would like to clarify and document this.
----
See: http://developer.apple.com/documentation/MacOSX/Conceptual/BPFileSystem/Articles/Aliases.html
----
Can one implement a cocoa app that does the copy/move etc file system operations either like 1 or 2 ?
----
To copy programmatically, take a look at: http://developer.apple.com/samplecode/FSFileOperation/index.html
----

Either way there seem to be huge implications , and i am having a hard time documenting them.

