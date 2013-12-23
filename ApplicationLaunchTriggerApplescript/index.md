---
layout: page
title: ApplicationLaunchTriggerApplescript
---



I'm newish to Cocoa, but advanced in AppleScript and I have a problem. I use ACGI Dispatcher to trigger AppleScript from the internet. I also have a xServe on the internet where people can upload ACGI. The thing is I need a way to stop unapproved ACGI from running.

So I turned to cocoa, I thought I subscribe to launch of a application (or process) event then triggers an AppleScript (since I know Applescript) on launch and check a list of approved applications and their modified date and location path (and/or some sort of enbedded plist flag). If all match then allow application to run.

It could be useful for parent control. Blocking Applications.

Who can guide me to how to set up triggers from any application. Like Safari starts a download (maybe plugin method). FileMaker record is committed or a field focus is changed. Applescript is all about controlling our application therefore you alway like way to trigger AppleScript when something happens. UI Action is an example of GUI watch and trigger, can someone guide me on technically how it works?

----
I found out parent control does this. But my Applescript Application don't come up as controllable, not in the list. AppleScript Studio Application do, But I can't get ACGI Dispatcher to talk to an AppleScript Studio Application. You can make a normal Stay Open AppleScript Application work with parent control as an iMagine Photo Script does come up in the list.

Still like to know how to block application myself.

