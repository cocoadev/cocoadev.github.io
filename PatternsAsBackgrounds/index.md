---
layout: page
title: PatternsAsBackgrounds
---

I'm trying to draw a pattern in the background of an NSScrollView with

if (backgroundImage = [[[NSImage alloc] initWithContentsOfFile:path] autorelease]) {  [scrollView setBackgroundColor:[NSColor colorWithPatternImage:backgroundImage]; }

everything works fine except for the pattern always starts at the bottom of the view, and when you resize the scrollview, the pattern looks like it is "stuck" to the bottom. It would look much cleaner starting from the top. I have tried using a subclass of NSScrollView with isFlipped set to YES, and I've also tried flipping the ScrollView?'s clipView to no avail. Does anyone have a solution? I would rather not have to do something goofy like draw out the pattern manually in another view.

