---
layout: page
title: SimulateKeystrokes
---

I am trying to simulate keystrokes to the system, whether this is done in Cocoa or Carbon, I don't care.  What makes this "hard" is that I need to simulate the SHIFT key, not as a modifier, but the shift key-press itself.  Apple released a EventMonitorTest, which allows you to see the actual key events, and from there i know that the keys have a value of 0x200 for a shift key down, and 0x0 for shift key up.  From that I guess that the system responds only to the 0x200. 

Anyone know how to simulate a shift keystroke?

(the end goal of this is to run StickyKeys and MouseKeys from an application (not through the Preferences Panel)

