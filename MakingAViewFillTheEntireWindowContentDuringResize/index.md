---
layout: page
title: MakingAViewFillTheEntireWindowContentDuringResize
---



Hello,

Everyone here has been very helpful the last month as I learn Cocoa. Thank you.

I have one question which seems relatively simple. My program basically has the normal title bar and one big custom NSView taking up the entire window's content view. I would like the user to be able to resize only in the x-direction (currently resizing is disabled). When this happens, my custom NSView should still take up the entire window's content view.

To be honest I'm not sure how to do it. I know that the window's contentView must come in somewhere.

----

You "know" incorrectly. :-) What you want is most easily done in Interface Builder. Use the size inspector (Cmd-3 in IB) to inspect the sizing of the window itself and set its max/min **height** to the same number. Inspect the size of the view you want to follow along - set the sizing springs to follow both height and width (the inside set of springs should be enabled, all others disabled). The objective is to have the view follow the window's size, but limit the sizing of the window itself to only allow the width to change.

----
In my experience, IB for some reason doesn't like having the min and max be the same number, it wants them to be different by 1. This will allow the user to resize the window by 1 pixel. This is easily fixed by setting the proper min or max in code in your     -awakeFromNib method.
----
Thank you both very much! I never even noticed the inside set of springs before.

