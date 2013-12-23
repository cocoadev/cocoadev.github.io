---
layout: page
title: NSViewOverlappingWeirdness
---



I have a custom NSView where I draw several objects. I add to the NSView 2 standard buttons. Everything draws perfectly, but when I click on the buttons, and subsequent mouse moves on them, the view appears to be drawing objects repeatedly without clearing it. Here is an example of how text is supposed to be drawn, and then how it gets all funky:

http://img291.imageshack.us/img291/9121/untitled1hs8.jpg

I tried using NSRectFill() to clear the view with white, but that doesn't help. Is there something I need to be doing with the NSButton in order for its superview to draw correctly? Thanks.

----

Well I fixed it - in drawRect:, I check to see if the passed rect matches one of the button's frames, and if so, I don't draw anything. Is there a better way to fix this?

----
Yes, ignore the passed rect entirely. You're using it as the area to draw, but it's just telling you what area needs to be *redrawn*, which in the case of the buttons is a small area around them. The only reason to use the rect parameter is to optimize your drawing by not drawing objects that fall outside of it, and this is entirely optional.

----

Well in my case, the area around the buttons doesn't need to be redrawn. I know it's bad, but if I ignore the passed rect and continue drawing, then I get the weird behavior mentioned above.

----

Time to PostYourCode, I think.

----

The above effect occurs when you draw the same thing multiple times. It's especially noticeable with text because the antialiasing around the edges adds up and creates the grittyness (for want of a better term) you see. It happens because of two things. 1. your view by default is assumed to be transparent, and 2. your drawRect is not erasing the background. So every time it gets an update, it's just drawing on top of pixels that it already set last time around. The fix is trivial - just erase the update rect to your background colour at the start of drawRect:. The window buffering ensures that you never see this erase step - it won't flicker. If you're drawing directly onto the window background and don't want to erase, there is a slightly more involved way to ensure that the background gets updated (erased) before you get called to draw, to prevent this effect. Off the top of my head I forget the steps needed, but the reason is the same - you're drawing the text multiple times.

----

It's because your button frames overlap.  Overlapping sibling views are not supported.  The view machinery will not accurately calculate what needs redraw at what times.

----

Luckily, overlapping views are supported in Leopard. (Publicly available info, if you do a bit of detective work on the State of the Union.)

----
Since when is the State of the Union public? (Remember, even free ADC accounts get NDA<nowiki/>s.)

