---
layout: page
title: OpenedFromFile
---

Hello,

Does anyone know of a way to check if a non-document-based app has been opened from a file (e.g. by opening an associated file type in Finder) before application:openFile is actually called?

I thought of checking [[NSProcessInfo processInfo] arguments] to see if it has more args than usual, but I wasn't sure how reliable this would be.

Thanks for any help!

*What are you trying to do?*

Basically, my app saves all its data between sessions.  So when it is run I need to know whether it has been run normally (in which case I want to display the previous session data) or run by opening a file (in which case I don't want to display the session data, just the opened file data).

And because my session data is restored in awakeFromNib of my mainWindowController, I'm trying to find a why to skip the session restore if application:openFile: will be called right afterwards...

*I would suggest redesigning things a bit, and restoring your session data from     - (BOOL)applicationOpenUntitledFile:(NSApplication *)sender;, which is pretty much what it's for. That should make everything work as expected.*

Thanks for the suggestion, I gave this a shot but applicationOpenUntitledFile: never gets called.  Is this still supposed to be called in a non-document based app?  You suggestion does seem like a better way to do things if I can get it to work...

*I'm almost certain that it is, and this is how NSDocument-based apps implement stuff, by using those methods. I'm not sure why yours isn't getting called, but I suggest investigating and possibly implementing     - (BOOL)applicationShouldOpenUntitledFile:(NSApplication *)sender;. It's possible that if you don't implement it, the default behavior is NO, but again, I'm not sure.*

This is very strange...I've tried implementing both applicationShouldOpenUntitledFile (which the docs say does return YES by default) and applicationOpenUntitledFile with an NSLog in each, and neither gets called.

I also noticed that if I remove my applicationShouldHandleReopen delegate, close my mainWindow, and click on the doc icon, both applicationShouldOpenUntitledFile and applicationOpenUntitledFile DO get called (but not when applicationShouldHandleReopen exists, I assume because this overrides the newDocument behavior).

But oddly enough, neither of the "OpenUntitledFile" delegates get called at startup.  I'd love to hear any suggestions...I'm pretty stumped.

----

In the main NIB file of your application, check the window's attribute "Visible at launch time". If this is on, app's delegate method applicationShouldOpenUntitledFile is not called. -- MichalBencur

