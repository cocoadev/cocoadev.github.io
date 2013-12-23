---
layout: page
title: OpenFileInFinder
---

I know I would use Applescript for this, but does anyone know the exact script to open a selected file in a new finder window, aka acquisition, omniweb, etc.

----

Well if your feeling confident you could download the source code for LimeWire (http://www.limewire.org/) and see how they do it. The downside however, is that LimeWire is written in Java, not Cocoa.

----

And limewire is a convoluted mess.

----

    
    id workspace=[NSWorkspace sharedWorkspace];
    [workspace openFile:*full pathname* withApplication:@"Finder"];


This will open a new window if a finder window isn't already displaying the contents of the parent directory for the selected file. If there is a finder window that is already displaying the contents of the parent directory, this action will make that window key and order it to the front while keeping your application just behind this one finder window (i.e. all the other finder windows will stay behind your application windows). 

--zootbobbalu

**But this doesn't select anything in the Finder window.**

----

That's strange, it does for me.... Are you giving just the path to the parent directory or the full path to the file you would like the Finder to highlight?

--zootbobbalu

----

Thanks for that, works perfectly.

----

**Sorry, my mistake; braindead moment.**

