---
layout: page
title: ChangingWindowTextureProgrammatically
---



Hi,


How do I change a window's brushed/aqua state programmatically? I'd like to offer an option between Aqua and Brushed metal for my program's users.






Thanks!
JohnWells

----

AFAIK there's no way to do this 'on the fly'. You need to create a new window with NSTexturedBackgroundWindowMask

----

What other applications do is store the value the user enters into the prefs, and prompts them to restart the app.  When the app is started your custom window class checks the prefs class and draws the background accordingly... some app called Movie Gallery or something used this method.

Dan Wood of Watson fame posted this that may be of some help:
http://weblog.karelia.com/Cocoa/Class_Posing_is_Cool.html

*I think there is a private method -setTextured: in NSWindow* 
----

I used something like
    
	BOOL canWeChangeTextureOnTheFly = theWindowController window] respondsToSelector:@selector(_setTexturedBackground:)];
	if (!canWeChangeTextureOnTheFly) return;
	[[theWindowController window]  _setTexturedBackground:defaultTextured]; //or whatever,  undocumented !!

I can't remember where I got this, probably from this helpfull site, it not my own invention, but it works,
[[PaulCD
----

