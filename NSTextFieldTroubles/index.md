---
layout: page
title: NSTextFieldTroubles
---

For some reason out of the blue, I cannot connect to an NSTextField in my NIB. I have checked everything I can thank of. It is still connected in the NIB, it is still declared, and yet I still cannot get a value from or send a value to the NSTextField. Does anyone have any idea what is going on?

----

Follow these steps, and it should become relatively easy.

1)  Open the NIB file in IB.

2)  Create a new controller class

3)  Make an outlet to the textfield

4)  Instantiate the class

5)  Draw a line (by holding control and dragging) from the instance of the class to the textfield.

6)  Double-click the outlet to connect.

Now you should be able to do something like [textField setStringValue:@"Hello world."];


----

I guess I should rephrase my problem. I have it connected in the NIB, but I cant access it in the code using something like:

    
size = [downloadSize floatValue];


and I cannot set the string value using something like:

    
[downloadSize setStringValue:@"Hello world."];


I'm not getting a value for size, and it is not setting the string to @"Hello World". It has worked perfectly for months until a few hours ago.

----

Assert at runtime that downloadSize != nil. Then make sure you're not assigning something to downloadSize by accident. After that, reload the header file for the class of yours that you're connecting (reload it in InterfaceBuilder by double-clicking on the instance and then when the class is selected in the next pane, use the contextual menu), reconnect if you have to, and try it again.

-- RobRix

----

I dont know what the problem was, but I finally fixed to by creating a new project and rewriting from scratch all the code that that one NSTextField used. Everything now works properly.

-- JacobHazelgrove

