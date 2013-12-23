---
layout: page
title: VisualEffectsInCocoaApps
---



Brought over from the hotly-debated General/NewsFire page ...

The suggestion was to create a page where all of the effects in the app, and techniques to recreate them, are discussed.  This is completely relevant to Cocoa programming, and General/NewsFire is a good example of "superfluous visual effects."

----

The most noticable part is the smooth reordering Table View.  When rows are reordered or sorted, they glide to their new position as opposed to snapping.

----

Then we also have the fading collapsing in the Table View -- is it really a Table View, or some kind of Outline View?

*What 'fading collapsing' are you referring to? I'm not sure I know the effect you're talking about (I'm a regular General/NewsFire user). ... Ah. Never mind. The OP is referring to the "Groups" feature where you can add several feeds to a group. Opening or closing the group slides the rows below it up or down smoothly, then fades the children in or out. It's clearly an outline view, just subclassed (undoubtedly using private General/APIs - then again, Mail.app does the whole gradient row highlighting thing too).*

----

Then on the right, there is a neat looking list that highlights the item the mouse is over.  I'm 99% sure this is a Web View where the HTML is generated dynamically in the app.  Interesting.

*This is covered in General/NSTableViewRollover*

----

It also does a number of other things - for instance, when you quit, the app fades out. When it isn't active, it fades slightly. It's very focused on visual response.

*Window fading (animated):* http://www.scotlandsoftware.com/products/source/ *(see "Window Fade-out" example).*

----

I am very interrested in recreating some of these cool effects. I am a little more interrested in recreating iChat's groups because to me, they are much nicer than General/NewsFire's. When you collapse a group in General/NewsFire, the groups below it smoothly move up, but the items in the group you are collapsing fade out. In iChat, they items disappear into the group. Very slick. And he definitely uses a General/WebKit for the main view. Click on something and drag and it should become obvious. If you want to see the source, I believe it is in Resources/Themes/ or something (I don't have a copy. I dumped it when it started costing 20 bucks!).
--General/ZacWhite
