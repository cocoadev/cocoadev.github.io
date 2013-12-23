---
layout: page
title: ActionMethod
---



An action method is simply a method (see: InstanceMethod) that is attached to a control object, either in InterfaceBuilder or programmatically. When the user interacts with the control, he causes it to invoke the method; hence, the term 'action method'.

This takes most of the gruntwork out of building a modern GraphicalUserInterface (GUI) application.

Here is a simplified example; if you have a window with a button, and you want to sound a beep every time the user presses the button you would:

1. Define a method called -beep that beeps when it is called, somewhere in a class. In order for a method to be recognized by InterfaceBuilder as an action, it must take one parameter (an object) and return nothing.
Example: -(**void**) beep: (**id**) sender;

2. Create an instance of the class in InterfaceBuilder (as well as the window and the button).

3. Press the "Control" key and draw a line from the button to the class instance in InterfaceBuilder, indicating a connection between the two.

4. InterfaceBuilder asks which action you want to connect the button to.  Select your -beep method.


That's all there is to it. 

----

To expand on how IB recognizes methods as actions:



*It looks for     IBAction as the return value.

*It looks for a     void return value and a single parameter of type `id` named sender



So if you use     IBAction you can statically type your sender parameter.

It seems like there's more to this... I remember a very explicit listing of exactly what IB looked for, in what order, posted by a membre of the IB team at Apple to a mailing list, but of course, I can't find it now.

