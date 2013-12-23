---
layout: page
title: OpeningDocumentIniCal
---



Basically, I have a Save Panel, that saves a .ics file.  What I want is to be able to, once the file is saved, automatically open the file in iCal, not my own application.  The path to the file can be a standard place (like ~/Library/Application Support/myApp/), but it would be nice if it could use NSSavePanel fuctions directory and filename to open it.  Thanks for the help...

----

Well, I dont know about getting the Save Panel to open the file, but you can ask NSWorkspace to do it for you. [[NSWorkspace sharedWorkspace] openFile:fullPath withApplication:@"iCal"]; fullPath should be the path to the file you want to open. You can get it from the save panel by sending it the message, [mysavepanel filename]; --CurtisHoover

----

awesome.  turns out, i dont need the Save Panel after all.  i just did [[NSWorkspace sharedWorkspace] openFile:[@"~/Calendar.ics" stringByExpandingTildeInPath] withApplication:@"iCal"]; and it worked like a charm.  thank you very much!

Right, the only thing the open panel is for, is to find the file in the first place. If you're sure it works to hardcode the path, that works too.

----

Yeah, basically, I automatically created the .ics file, and then wanted to open it immediately.  Since I automatically created the iCal file, I just placed it in ~Library/Application Support/myApp/, and that way, the user never sees the file.  And, therefore, I don't need the Save or Open dialogs.

