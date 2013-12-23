---
layout: page
title: HowToCreateDisclosureTrianglesInCocoa
---



You can steal a disclosure triangle from another app's .nib.  For example, there's one in:     /Applications/Utilities/Process Viewer.app/Contents/Resources/English.lproj/ProcessViewer.nib
It's just a toggle button with a left-pointing and a down-pointing icon.

----

I don't have an answer for this one yet, but I'm looking.  The basic idea is that I see this UI element everywhere, yet can't figure out how to use it in my Cocoa app.  I've got a window that I want to have a collapsed and an expanded state, kinda like the save panel or the Classic Startup window, or the software update dialog.  I've managed to hack together my own expand-and-reveal/hide-and-collapse code, but I'm missing the disclosure arrow.

Anyone?

-- LeslieOrchard, 27 Jul 2001

It's not a UI element in Cocoa. You can fake it like PPP connect does, just have a look at their nib -- it's basically a button that changes its icon. This has been discussed several times on the cocoa-dev list at lists.apple.com, btw.

-- FinlayDobbie

Thanks, I'll take a look there.  I *am* on the cocoa-dev list and asked about it, but didn't seem to get any answer.  Figured I'd ask here too :)

-- LeslieOrchard

----

In Panther, the disclosure triangle is a type of NSButton. What I would like to know is an easy way to resize a window with it.

----

When the button clicks, resize the window. There is no easier way than that.

----

You want to do     window setFrame:newFrame display:YES animate:YES

----

There's some discussion of window resizing at WhereIsCoolSystemPrefsWindowEffect.

