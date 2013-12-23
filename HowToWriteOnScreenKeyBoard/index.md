---
layout: page
title: HowToWriteOnScreenKeyBoard
---

I was wondering how I would write Mircrosoft's on screen keyboard program for mac os x.
My main question is how to make the app interact with all other app's that accept text input ?

----
System Preferences->International->Input Menu->Keyboard Viewer, Show input menu in menu bar.
Then, Menubar->Input Menu (looks like a flag usually) -> Keyboard Viewer.
I'm not familiar with Microsoft's, but does that save you a bit of work?
----
And to address your secondary question, are you looking to see all keyboard input for the current user?  That's possible with the event tap API, http://developer.apple.com/documentation/Carbon/Reference/QuartzEventServicesRef/Reference/reference.html .  Note that the API is unusable unless the tapping process is run as root and the user has explicitly turned on access for assistive applications from System Preferences.  Basically, it's a security hole if a non-privileged process can intercept keyboard input.

Since we mostly are not windows users, you will have to explain better if we aren't answering your question.

