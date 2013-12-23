---
layout: page
title: SharedColorPanelColorSpaceChange
---



My application makes use of a sharedColorPanel.  I get a colorChanged message when the color changes in the colorPanel, but how do I know when the color space changes, from RGB to CMYK for example, if the color sliders aren't moved?

Thanks.

----
I don't believe it was designed to work in this way. A color is a color; you can ask the color for its space or space name, however. Why do you need to know the space as it's browsed as opposed to when a color is picked? What are you trying to accomplish?

----
The application makes color swatches.  Someone may, for example, select an RGB color.  The program will produce an array of color swatches.  If the user then changes the color picker color space (in the colorPanel) to make CMYK swatches around that same color.  If they simply change the color space that way, a new color is not really "chosen."  I hope that I am explaining what I am trying to do clearly.

----
You may need to re-think your approach. You cannot rely on the color picker to report when the user is simply "browsing". It only reports when a color is picked. This is analogous to the open panel; there's no public API for being notified when the user browses to a different directory or volume - you're only notified when a selection is made (or canceled).

----
Understood.  That is how it works.  However, since the a chosen color has a colorSpace, when you change the colorSpace in the panel for a color already selected, that change is not reflected in the returned color.  The user has to select a new color.  Well, that behavior wasn't expected by me, in any case  ;-)

----
Actually, the color picker is pretty different from an open panel, and in fact its entire mission is to report while the user is browsing. There is no OK button or anything else to indicate when the session is over (except the close button, but using that to indicate that the color has been definitively chosen would be horrible). Just like the font panel, the color picker is intended to reflect changes made by the user in real time. Unlike the font panel, it seems that the color picker has a change the user can make which does not actually generate any notifications to the client object.

----
So, based solely on this evidence, I'm not crazy.  Well, not crazy in how I think the panel "should" work.  I suppose, then, that I still have to rethink how I handle this, as the panel itself won't help me out.

