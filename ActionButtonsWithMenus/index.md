---
layout: page
title: ActionButtonsWithMenus
---

In Finder and Mail among other applications, there is an action button that has a jagged wheel on it and a small arrow pointing downwards.
Pressing this button will invoke a menu attached to the button, this resembles the popup menu that exists in the NIB tool however, trying to add this scheme in IB results in an excess space and it ends up looking rather ugly..

Is there a trick in cocoa to fix this?

Thanks..

----

It's usually best to do this sort of thing with your own custom images. The whole button is an image. Two images, actually: one in the normal state and another in the pressed state.

----

Yes I know, but the only way I can seem to attach a menu to this is the ctrl-click and not the "normal" click way.. is there another way?
I have the custom buttons nailed... :)

----

Can you please describe what you are doing better?  It is difficult to debug if all you say is that you are using Interface Builder and it doesn't work.

As a guess, try using an NSPopupButton instance instead of an NSButton instance.  In the palette, this is the button with "Item1" printed on it.  You can change the attributes to make it look more like the kind of button you're after. 

----

When I created a custom control that needed to pop up a menu in certain circumstances, I simply had it contain an NSPopUpButtonCell. I never asked it to draw itself, so the user never had to see it, but I could pass it clicks and it would show its menu when I wanted it to. It works perfectly, and it gives you good control over the location of the menu.

----

I guess creating a cusom palette is the answer?

To clarify my quest, using a NSPopupButton with the Item1 you can give it an image for the face of the button, but it adds som space outside the image no matter what I do in IB.
I will try to attach an image when I figure out how to do that :)

Picture
http://homepage.mac.com/noonas/images/NibPopUpButton.gif

----

You may be able to override     -menuForEvent:. Otherwise, the only way I know of to arbitrarily pop up menus is with the Carbon MenuManager.

----

No, no need for a custom palette.  To reiterate a previous suggestion, read the documentation for NSPopUpButtonCell.

