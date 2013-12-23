---
layout: page
title: ExtendedSaveDialogInEveryApp
---

Ok� I want to extend the save dialog and open dialog of every app to include some cool new stuff I created. How? I was planning on building a category for the classes and using the NSSavePanel - (void)setAccessoryView:(NSView *)aView method, at least for now.

But how do I get every application to use this category? I was hoping for something nice like the Input Managers or the Service Menu things, but that doesn't seem to be happening. Can anyone hint me?

 I know that Default Folder X (or whatever it is called) somehow catches every program that starts and extends the file save and load dialogs as a program launches. It is not nice, but works, but how should one do that? 

----

You can use APE from Unsanity, or check out extendamac http://extendamac.sourceforge.net/

----

But how are you going to manage those extensions ;-)

