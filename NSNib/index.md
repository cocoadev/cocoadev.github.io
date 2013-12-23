---
layout: page
title: NSNib
---

[http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/Classes/NSNib_Class/]

Instances of the NSNib class serve as object wrappers, or containers, for Interface Builder nib files. An NSNib object keeps the contents of a nib file resident in memory, ready for unarchiving and instantiation.

Only available in 10.3 and up.

----

Unlike what you could hope, the NSNib object does not provide any way of inspecting the hierarchy of the .nib file; it only provides initialization methods that load the nib from disk and instantiation methods that unarchive and initialize the objects in the .nib. You may pipe the output of the /usr/bin/nibtool program to inspect a nib without instantiating it.

----

Use NSNib when     [NSBundle loadNibNamed:owner:] doesn't work, like for the rare CLI app that needs to load a nib. ;)

