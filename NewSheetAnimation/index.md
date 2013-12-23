---
layout: page
title: NewSheetAnimation
---

Okay, so Panther brings a new sheet animation style (where the sheet seems to curl out), but I can't see how to get my current sheets to do this.

Anyone have any idea? -- GarrettMurray

----

You get the old animation for your program??? there is only one animation on my system, and no way a program can choose between the two. However, I think that if the sheet is either very wide or very narrow, it may look different.

----

Yeah, I think it's because I have small sheets (only like 300x50). They slide out normally. It's not a big deal--I can live with it. --GarrettMurray

----

Jaguar:  Two animations:

* normal:  sheet slides straight down
* alternate:  if the sheet is wider than the window, then it will be similar to the Dock genie effect


Panther: Two animations:

* normal: sheet slides straight down.  This is only for windows with titlebars! (and perhaps for toolbar customization in other windows,  haven't tested that)
* alternate:  the "curl up into the third dimension" animation... this one is used in two scenarios:

* when the sheet is wider than the window (instead of the older genie effect)
* in metal windows (where there is no titlebar, so the straight down effect looked odd) ... in this case a "slot" is also drawn, and the sheet looks like it is coming out of the slot. (Thus answering the old question of "where do sheets come from in metal windows?")


--TimBuchheim

