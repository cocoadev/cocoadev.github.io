---
layout: page
title: ExtendedFileAttributes
---

Tiger includes support for the "xattr" family of low-level C 
functions in the BSD layer: getxattr(), setxattr(), removexattr(), listxattr()

----

Small application with sample code (including a cocoa wrapper for the C) now available from:
http://www.macs.hw.ac.uk/~rpointon/osx/xattr.html

----

Some more info at:
http://arstechnica.com/reviews/os/macosx-10.4.ars/7

And workaround for kernel panics when using it on AFP mounted volumes:
http://macromates.com/sigpipe/archives/2005/07/20/metadata-workarounds-on-tiger/

----

What's the benefit of xattr? is it faster than using nsfilemanager?

*There seems to be a new NSFileManager attribute key, NSFileExtendedAttributes,  that allows you to get some of the extended file attributes - though it seems to omit the FinderInfo and ResourceFork values.*

