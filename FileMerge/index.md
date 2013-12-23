---
layout: page
title: FileMerge
---

FileMerge is an application that allows you to visually compare differences between two source files.  You can see where lines of code have been added, removed, or changed.  You can also edit either of the files being compared.

It is used by ProjectBuilder for CVS operations.  See CvsBasics for more information.

----

Is there any way to make FileMerge always save over the local copy when edits are made to the local copy of the files being merged or compared?  I am trying to use it for looking at SVN diffs before committing and it visually does it better than anything else I have seen but I also want the ability to save over the local copy and re-diff so I can reject any superficial changes debugging code that I forgot about before a commit.

----

Why hasn't anybody created a Cocoa-based directory merge tool? (that compares 2 set of directories, and allows you to launch FileMerge to compare individual files).  It seems like this should be pretty trivial right?  You should be able to use the SimpleBrowser demo from Dev Tools as a basis for this. Call it DirectoryMerge.

----
Because, niftily enough, FileMerge does. Just use two directories instead of two files.


----
I find this really useful to have in my .bashrc:

<code>
function filemerge { /Developer/Applications/Utilities/FileMerge.app/Contents/MacOS/FileMerge -left "${1}" -right "${2}" & }
</code>

so then you can just use it like:      filemerge /tmp/foo /tmp/bar 

----
If you have FileMerge installed, then you probably also have      /usr/bin/opendiff  installed as well.       opendiff  launches FileMerge (or connects to an existing instance if it's already running) and returns immediately, so you can define your      filemerge  function as an alias:

<code>
alias filemerge=/usr/bin/opendiff
</code>

     opendiff  gets installed along with FileMerge as part of the Mac OS X Developer Tools.  More info is available on its man page.

