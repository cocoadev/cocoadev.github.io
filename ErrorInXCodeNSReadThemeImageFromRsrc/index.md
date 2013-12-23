---
layout: page
title: ErrorInXCodeNSReadThemeImageFromRsrc
---

One big problem here, I suddenly get in my program an error: _NSReadThemeImageFromRsrc  - index -8 for 'pxm#' 2710 out of range (must be between 0 and 11)
The result ist, that one of my own NSView components will not be displayed anymore. I tried to change the nib, but it doesn't seem to be a problem with the nib file.
I couldn't find any hints in the web. Anyone out there who had the same problem?

--ThomasSempf

----

Okay, one of the topics where I answer myself (at least a little bit...). I found out it was a problem with my function call to [NSView scrollPoint:NSPoint]. I delivered false Point information. Still the error message seems to be strange to me...

--ThomasSempf

