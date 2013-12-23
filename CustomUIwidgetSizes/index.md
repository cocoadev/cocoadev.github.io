---
layout: page
title: CustomUIwidgetSizes
---




Can anyone tell me how to go about contraining the size of a custom widget by using NSControlSize. I understand that it requires using NSCell, but i dont know how to set the maximum and minimum sizes that the control can be resized to. Very shortly my controls will be going into an IBPalette, and i want the size to contrain like it does with Apple's widgets (ie. NSButton's or NSProgressIndicators not resizing thicker than their maximum thickness.

Thanks in advance, -- Super Freak

*Overriding setFrameSize:?*

*And/Or     sizeToFit*

Well, using setFrameSize: would make it a fixed size. You could resize it at all. I want to be able teo resize the width of the item. Size to fit is really only for sizing a control to the length of strings that are used in it's contents, like with NSPopUpButton. But in either case, neither of them would make the control work properly in IB (ie. you cant resize a progress bar to another thickness).

-- Super Freak

----

Using setFrameSize: would only make it a fixed size if you overrode it to do that. You could override it to allow free sizing of the horizontal if that's your goal.

----
In     sizeToFit you can grab the frame, validate and change it as needed. It's not just for text controls.

