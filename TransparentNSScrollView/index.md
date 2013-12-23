---
layout: page
title: TransparentNSScrollView
---

I am using General/NSScrollView so that users can enter in Text on multiple lines and format it with whatever fonts/styles they want.
I want to be able however to make the View transparent so that if there is a background pictures for example it comes through.
I noticed that the General/NSTextField is by default transparent. I also tried setting the background color's opacity of the General/NSScrollView to 0% but all that does is set the background colour to black.

Any thoughts?

Trantiom


NEVER MIND... I figured it out.
I had tried the

[myOutlet setDrawsBackground:NO]

but the outlet was pointing to the General/NSTextField which is contained inside the General/NSScrollField. So even though the textfield was not drawing a background the scrollfield was. So I created another outlet which pointed ot the General/ScrollField and all was good:

[scrollFieldOutlet setDrawsBackground:NO];
[textFieldOutlet setDrawsBackground:NO];

Trantiom
