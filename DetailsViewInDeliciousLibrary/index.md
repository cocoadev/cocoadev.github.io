---
layout: page
title: DetailsViewInDeliciousLibrary
---

Does anyone know how Delicious Library implements it's Details view? I know it is all one custom view, with all of the fields drawn internally in a subclass. I want something similar in my app, but I can't find a good point to start implementing one. Does it draw an NSTextFieldCell for each item, with conditional statements if it has or does not have a value for that attribute? Then it could draw it over again as editable NSTextFieldCells while in edit mode. I guess I am just trying to get a good idea how this could be done. Address Book does it, also, but not as fancy. Drawing the controls would be easy, I think, but the layout of the view is what has me stumped. Any ideas? --LoganCollins

----

I, too, have been interested in that UI style, and it's been getting worked on by the Center Stage project people, as part of their Back Stage add-on. You can download the source from their sourceforge site. I've been poking at it, but it's slow-going. If you can make some of those controls and a layout like that, please post a good example, so that people here can see it. There have been a number of new pages created with questions related to this DL style over the last few days. We should probably get something out there to resolve all of those pages... and my curiousity. ;)  -- JasonTerhorst.

----

I am downloading the source for CenterStage so I can tear it apart. ;) At least it's open source and we can see it for ourselves. So many of the cool apps like Delicious Library are closed source. That makes it hard to duplicate these kind of giant features. End rant. Haha. Anyway, It seems like exactly what we need, as long as we can reverse-engineer it to our needs. If I get a good template-like layout working, I'll post it here for everyone. That would certainly make a lot of people happy. :) --LoganCollins

----

Yeah, isn't free enterprise a bitch?

