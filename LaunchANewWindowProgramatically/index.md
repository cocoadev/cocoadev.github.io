---
layout: page
title: LaunchANewWindowProgramatically
---

    

- (void)myAction:(id)sender
{
         [NSBundle loadNibNamed: @"myNib.nib" owner:self];
}

 

----

This just loads a nib. The window would have to be set visible on open for this to work.

*so **that's** what "Visible at launch time" means in the IB Info inspector for NSWindow attributes!!!*

A better way is to have your nib's FilesOwner class be a NSWindowController, and just send it a     showWindow message

----

Before you start loading nibs at will, you should take a close look at who owns who (in a memory retain perspective). If you don't know who owns who before you load a nib, then you are at the gateway of "Memory leaks from HELL!!". --zootbobbalu

----

And even if you do understand who owns what, you should really create an NSWindowController (or subclass) and let it do the loading and so forth, rather than trying to make the whole nib self-contained.

