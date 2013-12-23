---
layout: page
title: NSTextFieldIBActions
---

I have an NSTextField that is connected to an IBAction. This IBAction gets called even when the text field relinquishes the field editor (i.e. if you click on a table view). I only want to perform an action when the return key is hit, but nothing seems to give me this desired result. I hope I don't have to create a custom field editor to accomplish this. Any ideas?

----

In Interface Builder when you have an NSTextField you have the option of sending the action "On end editing" or "Only on enter". Switch to only on editor and that should give you the desired response.

----

How could you implement this behavior in a custom class.

Might NSControl's - (int)setActionOn:(int)mask do the trick?

I noticed that's not in the documentation for NSControl. Am I not looking in the right place?

What are the mask values? 

The docs seems to say it:
    
sendActionOn:

- (int)sendActionOn:(int)mask

Sets the conditions on which the receiver sends action messages to its target 
(continuously, mouse up, and others), according to the value of mask, and returns 
a bit mask with which to detect the previous settings. NSControl's default 
implementation simply invokes the sendActionOn: method of its associated cell

So, create a TextView in IB with the setting send action only on enter. Then use this method on it with a random argument, and back you'll get it's old stats, ie the send action on enter mask. -- EnglaBenny

