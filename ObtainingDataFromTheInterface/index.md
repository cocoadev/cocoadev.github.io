---
layout: page
title: ObtainingDataFromTheInterface
---

My program will store what a user inputs in a text box into a variable after they click the enter button. How do I set things up so that the data will go into one of my program variables when the user enters something in the text field?

----

    
- (void)textFieldChanged:(id)sender
{
    NSString *string = [sender stringValue];
}


Note that you named "textFieldChanged:" in your nib file. it could just as easily been called "superInsanity_KILL_KILL_KILL:", or whatever.

-- MikeTrent 

----

There's a little mumbo-jumbo to be managed in Interface Builder in order to set this up: You need to select the text field and set its property so that it sends its action when the user presses *Enter*. You do this with the Attributes view when you Show Info for the text field in IB. You also have to connect the text field to the     textFieldChanged:  action by control-dragging **from** the text field **to** a representation of the object that implements the above action. If you haven't yet got that "representation", find the class name in the IB class browser and **instantiate** it in the nib file. Then you will have something to connect **to**.

----

For an introduction to accessor method, try AccessorMethods or HowToTransmitDataBetweenClasses

