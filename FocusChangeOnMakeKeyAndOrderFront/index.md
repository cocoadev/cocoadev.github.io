---
layout: page
title: FocusChangeOnMakeKeyAndOrderFront
---

Hi there, I have a window with two text fields. When the window opens the first is the first responder. If I close it, and open it again (using makeKeyAndOrderFront) then focus has advanced down to the next textfield. Repeat, and focus is back.

I have noticed similar behaviour in other programs (i.e. close and re-open their window, and focus has advanced).

It seems for me the only workaround is to invoke makeFirstResponder *after* makeKeyAndOrderFront (if I do it before, focus will *always* be at the second textfield, implying that the advancement takes place inside makeKeyAndOrderFront).

Is this a bug in Cocoa, or something I do not understand about key view chains?

----

That sounds truly weird ... Make sure your nib file designates one of the text fields as the initialFirstResponder (ctrl drag from the window to the first text field and select initialFirstResponder). Also make sure your fields' nextKeyView values are set correctly, just for completeness. If the bug persists, then it would help to see some code ... 

-- MikeTrent

----

Setting the responder chain manually doesn't help -- I mistakenly said text fields, the problem is actually not present with text fields but only with combo boxes.

So to reproduce:


* make a new Cocoa application in Interface Builder,
* add two NSComboBox gadgets to the window (actually the second gadget doesn't need to be an NSComboBox, but it need to be able to be first responder),
* connect the 'New'-menu item to makeKeyAndOrderFront: of the window


Now test run the GUI -- if you close the window and re-open (using the 'New'-menu item) then the focus has changed. Do it again and it moves again... setting up the responder chain manually will not fix it.

