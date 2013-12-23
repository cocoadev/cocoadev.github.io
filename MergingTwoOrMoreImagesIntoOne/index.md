---
layout: page
title: MergingTwoOrMoreImagesIntoOne
---



I want to merge (in memory, not on the screen) two (or more) images into one and then draw it on the screen (as a Dock icon, for example). How can I do that? Thanks in advance for any help.
----
The NSImage class documentation will get you started.  The terminology you want is not "merge"; it is "composite".  Given two images, lock focus on one and composite the other on top of it.  You will need to understand the compositing modes available as well and transparency (alpha channel) and other graphics concepts.  Armed with the correct terminology, a quick search of this very site offers: ImageCompositing
----
It works! Thanks!

