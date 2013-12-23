---
layout: page
title: HowToBringOtherApplicationToFront
---

Let's say I want to bring Mail.app to front -- how would I do that?

If I use NSWorkSpace to launch it, it probably gets a re-activate event, which could cause opening of a new window and currently I have the problem that even though the application does activate, the windows are not brought to front.

----

You could always embed an applescript in your application using the NSAppleScript class. This is how I remove songs from iTunes in iSweep and it works fairly well. Given my preference, I would use an Objective-C framework or class api to connect to and manipulate iTunes, but I wasn't able to find one, so I had to rely on AppleScript for this part of iSweep's functioncality.

-- DaveGiffin

----

You can get the app's ProcessSerialNumber via NSWorkspace and then use Carbon ProcessManager calls to activate it.

----

AppleScript really is the easiest way here. Just execute it using NSAppleScript

    
tell application "Mail" to activate


----

Unfortunately AppleScript has a noticeable startup delay on "slower" machines. Try this on your machine (from Terminal.app):
    
time osascript -e 'current date'


So I've done the ProcessManager solution, which is (``anApp`` being the dictionary I get from NSWorkSpace):
    
struct ProcessSerialNumber psn = {
   anApp objectForKey:@"[[NSApplicationProcessSerialNumberHigh"] unsignedLongValue],
   anApp objectForKey:@"[[NSApplicationProcessSerialNumberLow" ] unsignedLongValue],
};
SetFrontProcess(&psn);

