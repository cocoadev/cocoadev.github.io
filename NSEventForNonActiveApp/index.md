---
layout: page
title: NSEventForNonActiveApp
---

Hi There,

I'm trying to create a word-prediction application but have it run in the background instead of the foreground. I've tried setting it as the FirstResponder but it only responds to KeyEvents when it's in focus. Is there a way for me to hook into and get NSEvent without being the active application? I've seen other word predictors do it just wondering how I can do it in cocoa.

Thanks

----
NSEvent only handles events once they're delivered to the active application. Once an event becomes an NSEvent it's too late. You will probably want to use CGEventTap, which allows you to get keyboard events while not the focused app.

