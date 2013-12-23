---
layout: page
title: KeyboardDriversInIOKit
---



I'm trying to write some keyboard drivers for OS X (and a logitech keyboard to begin with).  I've looked at a lot of code - especially with emulating keypresses using user-space code.  

What I want to do is to interpret keypresses in the keyboard and then based on the frontmost application do different things.  Before I start coding, I want to get a general idea what I should be doing, so I'll try to explain my thoughts here.

1) write a kernel extension that a particular application (daemon) will take a hold of
2) write a daemon that talks to that kernel extension and then based on keypress X, will simulate the appropriate keypress.

Also, besides USBProber and IORegistryExplorer, what are some good (software only - free preferably) utilities for USB debugging, etc.

Any pointers and ideas would help greatly.

----

Hello! Apparently, no one answered your question! But maybe you�re able now to tell us how you did it? I�m very interested in this subject.

**Here�s my question:** Is it possible to intercept the text input from one and only one keyboard and modify it before passing it to the text system? And if so, can/should it be made in a driver?

I�m trying to make a CueCat driver for OS X. Of course, a modified CueCat outputs regular digits and letters but

1) It swaps upper- and lowercase (in code128, for example, *David* would become *dAVID*)

and 2) if you regularly use a non-qwerty physical keyboard or keyboard layout, the output will be *dQVID*, and *12345* would become *&�"'(* with a French keyboard layout.

You�ll say I just have to switch to the American keyboard layout, but:

* it�s very painful,
* you always forget about it, then enter bad text with your regular keyboard,
* you always forget about it, then quit the program (cmd-Q) instead of selecting all (cdm-A) :-((
* Anyway, the upper/lowercase swapping remains�


I hope you�ll understand my question and give me some clues.
Thanks, Wdyp.

