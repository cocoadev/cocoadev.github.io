---
layout: page
title: MinimumHeightOfSheet
---

I have created an NSPanel in IB which I use as a sheet.

I want this sheet to have a resizable width, but fixed height. Unfortunately if I set the minimum and maximum height to the same value in IB, that value will be for the window *with* a title bar.

When the NSPanel appears as a sheet, the size is correct, but if I grab the resize button, the sheet will get the min/max size I've set in IB, which is 20 pixels too high (the height of the window title bar).

Is there any solution to this problem?

*Set your size to 20 pixels less than what you actually want?*

**Ah, thanks for the suggestion -- unfortunately there seem to be a lot of problems related to having a fixed height, since apparently some resizing happens when the NSPanel is used as a sheet (so it will come up the wrong size), some capping happens (based on min/max sizes) when the size is loaded from user defaults (so the size after restart isn't the same as before quiting) etc. (and I don't really know how much height is cutted by having the window as a sheet), so all in all it seems hopeless for me to make this work :(**

*I see what's going on now, I didn't quite understand the problem before. Have you tried setting the min and max programmatically somewhere? I'm not sure what the best place would be, though.*

And speaking of resizing windows, why can I set springs for windows in IB?

*It's to control how they behave when you change your screen's resolution.*

