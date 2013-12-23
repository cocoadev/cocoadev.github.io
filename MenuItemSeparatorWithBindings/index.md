---
layout: page
title: MenuItemSeparatorWithBindings
---

Greetings!

Is there a way to define Menu item separators in your model layer so that Panther's controller layer will recognize this token and create menu separators from an NSArrayController's contents?

Thanks
-- EliotSimcoe

BUMP

----

Can you clarify? You can use a value transformer, but I do not see the connection between a model, an array controller and the contents of a menu.

----

Well, I have a model layer that contains an array of "destinations" for images. These destinations are keywords for where an image will be used throughout the editing process performed by my application. For the sake of argument, let's call two of these destinations "Now" and "Later". In the view layer, I want a popup button with a menu containing "Now" and "Later", but I also want a the following menu items to preceed them in the menu: "All", "None", and then I want there to be a divider. So the final menu layout looks something like this:
    
All
None
----------
Now
Later


What is the desired approach for accomplishing this using the new controller layer in Panther?

Thanks for any suggestions :-)
-- EliotSimcoe

----

IB does not allow you to bind directly to the menu key of the NSPopupButton, but you can do so in code using     bind:toObject:withKeyPath:options:, bind it to the item array of your model and provide a value transformer in the options dictionary. The transformer should then transform an NSArray to an NSMenu.

I have not tried it, but I would assume it to work.

