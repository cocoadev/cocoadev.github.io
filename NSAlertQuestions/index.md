---
layout: page
title: NSAlertQuestions
---

Q: Is it legal to pass **nil** as window (when sending beginSheetModalForWindow:...)?

A: If you don't want a sheet in a window, use the NSRunAlertPanel function

*I should have been more clear � I want to bring up a dialog in     applicationShouldTerminate:, this dialog should not be modal, since I must return     NSApplicationTerminateLater, and making it modal for the nil window has so far been the only choice I have found.*

Q: How do I change the default button (I know that I shouldn't)?

A: NSRunAlertPanel will let you specify the text for the different buttons.  You can 'change' the default button that way.

