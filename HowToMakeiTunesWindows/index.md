---
layout: page
title: HowToMakeiTunesWindows
---

I'm wondering what Interface Builder items iTunes uses to create the Source and Now Playing sections of the app.

----

They are probably custom carbon views since iTunes is Carbon.

----

Thanks.  If it is indeed custom carbon, then I may have found an "approximation".  Drag a NSTableView onto a brushed metal window, and deselect all of the Display option.  This does leave out the Source  or Now Playing header, but you could always do that with an image, I guess.

----

The iTunes features such as gradient-effect ("source" style) tables are discussed in great detail elsewhere.
Textured windows (metal) are discussed also on many pages.
The fact that iTunes is Carbon is mentioned elsewhere.
A topic title such as the above begs to be linked to the best of the iTunes styling discussions.

*I think the more ways of saying the same thing (that all reference the One True Page on the topic), the better. I say, don't delete, just redirect. It's more likely to stand out in a search this way, which curbs the 'make a new page every time we don't search' just a bit.*

**heh, wise advice**

----

Actually, I am getting a small urge to develop a topic related to various "effects", given the recent attention paid in VisualEffectsInCocoaApps - I will look around and see if something this general is already under way...

