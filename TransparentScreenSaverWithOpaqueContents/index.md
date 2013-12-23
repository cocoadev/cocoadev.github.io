---
layout: page
title: TransparentScreenSaverWithOpaqueContents
---

I'd like to have my screensaver draw a transparent background, but with the contents that I draw myself showing opaque (non-transparent.) Is there any way to accomplish this?

All the General/TransparentScreenSaver code I've seen uses     General/self window] setAlphaValue:alphaValue] which causes **everything** to be drawn with alpha. I know this is possible in a normal [[NSView, but all my attempts to do it in a General/ScreenSaverView result in a opaque black background.
