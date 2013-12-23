---
layout: page
title: HowToMakeButtonsOnTheFly
---

I have seen in some NIBS lately from apple that buttons are now sliced up in three images.
left middle and right side of a button. It seems that in the code they are assebled .

Does anyone know how to do this? This would save a lot in Size for the NIB content.

----

Make your own button class, have it draw the images.

Why would including images you wouldn't have to otherwise *reduce* the nib's size?

----

First of all thanks I will look in to the class design., and what I meant was that having less graphics
would reduce the application size.

*but if you supplied your own button graphics, instead of using the ones from the framework, it would increase the size. or am i missing something here?*

Well yes, adding own button graphics will increase, but if you dont have to add more than one set per button styl/state it's a lot less than 2 x actual button with face...

*Using NSSegmentedControl might be easier, just saying :)*

