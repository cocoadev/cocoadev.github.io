---
layout: page
title: DontHideWindowWithApp
---

I have an app that is similar in nature to PowerPoint or Keynote, and allows on-the-fly editing, so the user will have the document they're working on as a window on the main display, with the presentation being shown on the second screen (a full-screen window). The problem is, I obviously don't want to have that presentation disappear from the second screen when the user hides the app to quickly jump to another app. Is there any way to prevent a normal NSWindow instance or subclass from disappearing when the user clicks "Hide" in the menu?

----

NSWindow has a bunch of "hide" methods.  Check out the "Controlling behavior" section in Apple's online documents about NSWindow.  I think "setCanHide:" is probably what you'd need.

