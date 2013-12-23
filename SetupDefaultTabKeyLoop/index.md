---
layout: page
title: SetupDefaultTabKeyLoop
---



Did anyone create code to set a default tab chain/key view loop?

I.e. go through each view and subview left-to-right, top-to-bottom and set the next/previous responder?

I'm asking for this code because when I manually add/remove subviews, the default key chain is lost, so I need to programmatically restore it.

----

If at all possible, you should use setHidden:YES/NO instead of adding/removing subviews. That way the key view chain takes care of itself automatically. Trying to splice views in and out of the chain is a job that may cost you your sanity.

