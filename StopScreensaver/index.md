---
layout: page
title: StopScreensaver
---

I need to stop the screensaver and/or wake the computer screen if it's off/dimmed. I need to do something like simulate mouse movement. How can I do something like this?
Thanks.

----
Try General/UpdateSystemActivity.

----
Hm, General/UpdateSystemActivity "undims" the screen, but it doesn't stop the screensaver. I've tried calling it with General/OverallAct, General/UsrActivity, and General/IdleActivity. Is there anything else I can try?

----
You can simulate mouse movement using General/CGRemoteOperation.h, although this may not work to stop the screensaver as the events generated are somewhat different from real mouse events. Also keep in mind that if the user has set the screensaver to require a password to unlock, this obviously won't be able to get around that.

----
Hm, maybe I'll just mimic pressing the Control key or something (any key that wouldn't really do anything). Now that I think about it, moving the mouse sounds kinda rude to the user.

----
Wait, I found a better way, using an General/NSEvent. I'll post it here in case anyone finds this useful.

    
General/NSEvent *event = General/[NSEvent mouseEventWithType:General/NSMouseMoved 
								  location:General/NSMakePoint(10, 10) 
							 modifierFlags:0 
								 timestamp:0 
							  windowNumber:1 
								   context:nil 
								eventNumber:nil 
								  clickCount:0 
								 pressure:0.0];
General/[NSApp postEvent:event atStart:NO];


This "virtually" moves the mouse, without actually moving the mouse. 

----
Does that actually work? I'm really surprised if it does; I would think that it would only fake a mouse move for *your app*, and the system wouldn't notice a thing.

----
Yeah, it actually does. I've only tried it with the screen saver in test mode (i.e., going into the Screen Saver preferences and clicking "Test"), but if it works there, it should work when the screen saver is actually running.

----
And you're not the screensaver itself, or a system preferences plugin, right? Does the mouse cursor actually move on the screen? That's very interesting to know.

----
Nope, just a regular application. And the cursor doesn't actually move.

----
DANG IT, I was wrong: it only works if you're previewing the screen saver in Test mode, not when the screen saver is actually on. I KNEW I should have tried it with the real screen saver, but the minimum time you can set for the screen saver to activate is 3 minutes, and I didn't have much time. This is kind of embarrassing... O_o

----
Just enter "/System/Library/Frameworks/General/ScreenSaver.framework/Resources/General/ScreenSaverEngine.app/Contents/General/MacOS/General/ScreenSaverEngine" in terminal to fire screen-saver immediately.

----
Cool, thanks. I'll be using that.

----
Ok, I finally found something that seems to work (with the real screen saver).

    
General/UpdateSystemActivity(General/UsrActivity);      // If we don't call this, the screen saver will just stop and then start up again.

General/NSString *source = @"tell application \"General/ScreenSaverEngine\" to quit";
General/NSAppleScript *script = General/[[[NSAppleScript alloc] initWithSource:source] autorelease];
General/NSDictionary *error = General/[NSDictionary dictionary];
[script executeAndReturnError:&error];



----
Gah, now that doesn't wake up the screen from sleep/"offness". I just can't figure out how to do this...
