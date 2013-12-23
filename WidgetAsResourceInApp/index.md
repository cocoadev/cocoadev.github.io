---
layout: page
title: WidgetAsResourceInApp
---



I want to add a .wdgt file (a Dashboard widget) as a resource into my application, so that I can have a menu item "Install widget..." that does this:

    
General/[[NSWorkspace sharedWorkspace] openFile:General/[[NSBundle bundleForClass:[self class]] pathForResource:@"My Widget" ofType:@"wdgt"]];


However, when I import my widget into General/XCode, it is added to my application as a folder, not just as the bundled widget that it is in the Finder.  Since it's a folder, I can't add it to my target's Copy Bundle Resources build phase, and thus it isn't distributed in my app's bundle.  Is there any way to get this to work?  Thanks.

----
Select "Make folder references" instead of "Create groups" when you add the .wdgt, and you'll get a reference to the widget that you can add to the Copy Resources phase.

----
Works like a charm.  Thanks!
