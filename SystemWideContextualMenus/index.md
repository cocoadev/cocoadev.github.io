---
layout: page
title: SystemWideContextualMenus
---



I've been looking to make a finder modification to add another contextual menu option but from what I've read around it seems that this can only be done in carbon.  Granted a lot of the reading I have found is from 10.1 and previous.

Anyone have any insight into this.  I am looking to make a contextual menu along the lines of General/FruitMenu or Stuff-it's contextual menu.

----
I'm currently working on a project that does exactly this.  Here's the project: http://cvsfinder.sourceforge.net/.  There are two issues with using Cocoa for this though.  First it's only supported in 10.2 and on.  Second, you have to be carefull of class names clashing.  At the beginning my plugin would always crash if loaded into Safari.  It turned out the problem was we both had a General/AppController class and the objective-c runtime was trying to use mine. 

There are some things you have to do when you're a plug-in that you normally wouldn't do in an App.  For instance you need to allocate your own General/NSAutoRelease pool.  If the host app isn't Cocoa you need to initialize General/NSApp (by calling General/NSApplicationLoad).  If you need anyspecific help let me know.

Some good links:

* http://developer.apple.com/techpubs/macosx/Essentials/General/CarbonCocoaDoc/
* http://developer.apple.com/samplecode/Sample_Code/Human_Interface_Toolbox/Mac_OS_High_Level_Toolbox/General/SampleCMPlugIn.htm
* http://developer.apple.com/samplecode/Sample_Code/Cocoa/General/CocoaInCarbon.htm
* http://developer.apple.com/samplecode/Sample_Code/Cocoa/General/SpellingChecker.htm

-- General/SaileshAgrawal

----
Haha this is kind of funny, you are developing what I was thinking about.  I use General/TortoiseCVS at work and was interested in making a mac version.  Although I thought General/HareCVS was a cooler name.  I'll check out your source and see if I could contribute any.

Bryan Haggerty
