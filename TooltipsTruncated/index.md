---
layout: page
title: TooltipsTruncated
---


This is somewhat a stupid question, but I hope I do get feedback on this matter. I'm quite new in the Cocoa development so I am not that knowledgeable on these matters. 
Why are tooltip displays truncated at times? Is this an OS Limitation? I tried searching the Apple Mailing Lists but I couldn't find any information about it.
As far as I know, it is the OS which controls the length of the tooltip display. I don't know if there are any General/APIs available which allows the user to set the size for the tooltip.
Thanks in advance for helping me out with my stupid question.

----

I don't recall ever seeing a truncated tooltip. Do you have an example? Is this in an app you're developing or what?

----

In Firefox, tooltips are truncated (unless you use the Long Titles extension), but this is a limit of Firefox, not of Cocoa.

----

I've seen this intermittently on 10.4 in a Cocoa app (reproduced on several machines), where all tooltips are truncated to single lines of around 30-50 characters, instead of displaying the entire tooltip text. Hard to reproduce, but one case where it often happens is: Have a Finder (or other) window in between the app's main window (at the back) and the Preferences dialog (at the front). Then switch (to?) the Preferences pane once or twice. We set the tooltips programmatically (earlier, not when switching the windows). There is no API for setting the size that I know of, and I haven't found a solution.

----
If width is a problem then you could insert '\n' chars to make it a multi-line tooltip.
