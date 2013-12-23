---
layout: page
title: ProgrammaticallyCreatedTextFields
---



I am hitting my head against a wall on this one...

I am creating new textfields on a window in the same way IB does... dragging and dropping them onto a window which creates a new instance:

    
newTextField = [[MyTextFieldSub alloc] initWithFrame: (location of drop);


Now I have a seperate Nib file (an inspector) that I want to specify my attributes, Width, Height, Title, X, Y etc... same as IB.

So to do this it needs to be a two way communication.

1) When clicking on the textbox it updates the values in the inspector.. I guess by overiding the mouseDown event in the subclass

2) When changing the values in the inspector, they are pushed to the selected (focused) textfield.

I've tried some things (thanks to chucker) with overiding the firstResponder on the subclass which allowed me to push values to the inspector using a button NOT the mouseDown method, but now at a loss on how to return the values back to the focussed instance aswell as being able to sed the values to my inspector from my mouseDown method. The best chucker came up with was returning the changed values, but it did so to all instances of newTextField using a notification:

    
- (void)inspectorValueChanged:(NSNotification *) notification
{
[self setStringValue:notification object] string;
}


The above shows me pushing back the string value only... I am really struggling with how to manage instances, and being able to communicate its attributes to text fields that are in other seperate classes... Please help :-) Thanks

steve[dott]reynolds[attt]gmail[dott]com

