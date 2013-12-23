---
layout: page
title: CustonIBPaletteFreeze
---

I tried to make my own IB Palette according to the description in http://cocoadevcentral.com/articles/000046.php. But just like the feedback of that article, when I drag my control to the window in IB, the control just freeze at the edge of the window and stay there!

Any suggestions?

Jeff

----

uhh... I did play with this a long time ago and also experienced the same, but the fault was mine. Either i claimed to implement something which I didn't, or I didn't implement something that I should have...

I found it very useful to run Console (in Utilities) while debugging, as IB will print some debug info.

What kind of control have you made? are you using associateObject:ofType: and have you implemented initWithCoder/encodeWithCoder for your custom control?

----

Thanks for your reply. What I am making is a very simple control, basically it is just a subclass of NSView and respond to a Mouse up event. I have implemented initWithCoder/encodeWithCoder, but I don't know what is associateObject:ofType.

Jeff

----

Thanks. You suggestion really helps.

I used the console to watch the error message, and found the following message:

2003-07-16 21:55:27.571 Interface Builder[772] Exception during drag from palette: *** -[NSKeyedArchiver encodeValueOfObjCType:at:]: this archiver cannot encode structs

So, the error was coming from I try to encode a struct: NSPoint, it seems that NSCoder can't encode struct directly. After modified the method to encode the members of structure one by one, everything is OK.

Jeff

----

associateObject:ofType: is in case the object in your palette is not an instance of your actual object. E.g. you could have an NSImage instance in your palette, but wanted IB to instantiate MyCustomView upon dragging.

Sadly Apple is not always the company with the best documentation: http://docs.sun.com/db/doc/802-2110/ (look in the bottom of the table-of-contents, here you'll find several chapters about extending IB).

