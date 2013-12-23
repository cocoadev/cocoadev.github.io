---
layout: page
title: GetReferenceToAppController
---

I'm extending Apple's Quartz Composer Player example, and I'm trying to neaten things up so I won't get overwhelmed as I expand.  I have an AppController class to handle the usual UI and logic concerns.  I've also subclassed NSApplication to capture key presses while the full screen opengl context is being displayed.  My NSApp subclass just overrides sendEvent: and wants to send a message to the appController when certain interesting events occur.  I can't figure out how to get a reference to the AppController instance that I've instantiated in IB.  Am I going about this the right way?  I can't seem to find any information on the topic.

----
Never mind.. I just used [self delegate] and made AppController the delegate of my NSApp subclass.

