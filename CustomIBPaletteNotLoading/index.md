---
layout: page
title: CustomIBPaletteNotLoading
---

Greetings, Everyone!

It's a beautiful day in central NY. All of the clouds have gathered in my office...
Anyway, I am writing a custom IB palette for a CoreImageUI framework and recently IB began logging "unable to find class CIControlsPalette" when trying to load the palette. The palette's icon is correctly displayed in the palettes toolbar in IB, but when you click on the icon nothing happens. Has anyone seen this error before?

Right now my code is more or less bare bones... so it is boggling my mind what could be the issue.
Any advice will be greatly appreciated.
Thanks everyone,

--EliotSimcoe

----

In keeping with tradition I solved my problem shortly after posting to cocoa dev. For any interested parties, make sure that if you change the Product Name of a build target, you also change the Executable to match in the Properties Info tab.

Happy trails,

-- Eliot Simcoe

*Congrats. Retired.*

