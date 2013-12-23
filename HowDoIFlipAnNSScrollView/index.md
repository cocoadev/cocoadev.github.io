---
layout: page
title: HowDoIFlipAnNSScrollView
---

I'm having some difficulties with an NSScrollView (such as the ruler starts at the bottom of the view) not to mention that I have to convert all my coordinates.

Is there a relatively simple way to do this as it would save me from lots of code and save me some hairs :-).

Bleh this really was a newbie question, but I'll put the answer here. Put the following in your subclass:

    - (BOOL)isFlipped
{
	return YES;
}

