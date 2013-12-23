---
layout: page
title: MultiRowedNSTabView
---

Once upon a time I wanted a Tab View with 4 rows of tabs.  Unsuccessful at finding a way of getting this functionality in the basic NSTabView, I tried stacking 4 Tab Views on top of each other and coding them to switch positions accordingly when selected.  It worked well...until...the new style tabs were introduced.  The old type were opaque and effectively covered the views behind.  The new views are not and don't.  Is there a simple way to make the tab views opaque, or, have multiple rows of tabs?  *--Migrant Firm Worker*
----
*Do you mean like in Windows? I personally dislike this "feature", especially when clicking on a tab in the back brings it in front of the others, changing its position. So, how are you going to use the tab view, anyway? (That said, I have no idea how to do it :) --JediKnil*

You should never put views directly on top of each other in cocoa, it isn't supported.  What you can do is nest your tab views (especially if you can do it in a way that makes sense) or design a custom control.  I also agree with Knil though:  This is Windows-ish, Mac users won't like it.  If you're doing a preference panel, do it like Apple's system preferences.

----

There's a reason NSTabView can't do this out of the box: it's a terrible interface. If you MUST do something like this, how about laying out X rows of buttons (or make a matrix of buttons), and having them swap views in and out of an NSBox? But try and come up with a better interface. Your users will thank you for it.

----
I had written a mac version of a windows program, hence the Windows-ish feel.  Thanks for the insight anyway.  *--Migrant Firm Worker*

----

That's understandable, but most people would say that it's more important to match the feel of the platform than to keep the app's UI uniform.  You won't sell many copies to the mac crowd if it feels like a windows app.

