---
layout: page
title: NSStepper
---

I'm trying to use a stepper to change the contents of an NSImageView. I set the max and min values of the stepper to match the number of images I have, and then hooked up its target to my controller, where it calls a method that changes the image to the one at index [sender intValue]. But this always returns 1. Shouldn't the stepper change its own value automatically? Or am I completely missing something?

- DigitalCow

Ok, I found the problem here. Apparently, there's a bug with NSStepper which causes it to be completely impotent unless its autorepeat attribute is set to on. While this is undesirable for my use, it's better than a control that always returns 1. Just thought I'd post this in case anyone else was having trouble getting NSStepper working.

-  DigitalCow

How does one seed the initial value for the NSStepper to use?  If a user types a value into a text field that the stepper is updating, how does one tell the stepper to increment or decrement based on the newly entered value?  

- JimCath

I think you can use its superclass' method setIntValue: and enter the field's value as the argument. Not sure but I'll check this out...

- Trax

----
It's not a bug with NSStepper. The documentation says: "autorepeat: YES if the first mouse down does one increment (or decrement) and, after a delay of 0.5 seconds, increments (or decrements) at a rate of ten times per second. NO if the receiver does one increment (decrement) on a mouse up. The default is YES.". - PietroGagliardi

----

How is that relevant to the stated problem? He's saying that it's always returning 1 as its value. Even if auto-repeat is OFF, it should still actually increment or decrement on each click, so it will return a range of values. -setIntValue should set its value, but also make sure you're setting sensible min and max values - if they aren't set up correctly they'll probably default to something that prevents the value changing at all. --GrahamCox.

----
Is there a way to programmatically set the initial value of a stepper?  I can see in IB3.0 you can set an attribute called "Current", which does set the initial value. But I need the value to be different sometimes. I tried [NSStepper setIntValue:] but that doesn't seem to work.  Anyone know what method gets called for the "Current" Attribute? Thx.
-Larvell
----

Larvell, be sure to adjust your min and max values for the stepper *before* you assign its current value with setIntValue:. setIntValue does work.
--KN
----

Clicking the stepper with autorepeat off also works fine for me too.

