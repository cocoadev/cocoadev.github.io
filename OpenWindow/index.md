---
layout: page
title: OpenWindow
---



I have made two windows in my nib file and I only want the second one display at certain time.  What is the function call to activate the second window?

----
I'm going to leave out the usual "one-window-per-nib" speech for two reasons: I've done this too, and someone else can deliver the speech better than I can:

    

[secondWindow makeKeyAndOrderFront:nil]; // the one argument is the sender, but it's ignored.



----

There's nothing wrong with having two windows in a nib *as long as the windows are related.* Just sticking the other window in there because it's convenient is where you get the speech from me ;)

