---
layout: page
title: NSColorOpposite
---

Suppose that I have an NSView filled with a color which my user picks from a color well - which means that we can make very little assumption about the color when we receive it. Now imagine we've drawn an NSTextView on top of it (without the background). This means that the insertion point (that little blinking cursor) may or may not be visible, depending on the user-specified fill color behind it. Now, we can easily change the color of the cursor using setInsertionPointColor:, but how can we probe the fill color to find an opposite color (either by brightness or simply clashing color) to use that will have enough contrast to make it easy for our user to see the cursor?
----
If you wanted a direct opposite, you can just rotate the hue by 180 degrees. Might be kinda garish though.
----

If you can actually control the drawing of the insertion bar (not sure if thats possible) you could use a blend mode. See CGContextSetBlendMode. 

file:///Developer/ADC%20Reference%20Library/documentation/Carbon/Conceptual/QuickDrawToQuartz2D/tq_other/chapter_3_section_5.html

BTW, Blend modes work for all drawing - not just images.

----

Adium's AIUtilities framework has code for finding a good insertion point color in 1.0 and later.

