---
layout: page
title: MultipleValueBindingSetProgrammatically
---

 I�ve got to set some bindings programmatically, everything was ok so far using the documentation but now I don�t find the recipe to program a multiple value binding without IB.

I tried:
    
[bindingOptions setObject:@"%{value1}@ / %{value2}@" forKey:@"NSDisplayPattern"];

[theTextField bind:@"displayPatternValue1" toObject:theObjToBind
    withKeyPath:@"result" options:bindingOptions];
[theTextField bind:@"displayPatternValue2" toObject:theObjToBind
    withKeyPath:@"total" options:bindingOptions];


But I got the error �[<NSTextField 0x34b750> valueForUndefinedKey:]: this class is not key value coding-compliant for the key displayPatternValue1.�

Has anybody some suggestion? Thanks.

Flofl.

----

Take a closer look at IB. It has "displayPatternValue" and "displayPatternValue2" bindings. There is no "displayPatternValue1".

----

I�ll try your trick but in **my** IB, there are  "displayPatternValue1" and "displayPatternValue2"!!

��

Done. "displayPatternValue" returns the same error�

----

Well the proper name is     displayPatternValue1, see http://developer.apple.com/documentation/Cocoa/Reference/CocoaBindingsRef/BindingsText/NSTextField.html

I just tried the following code:
    
NSDictionary* options = [NSDictionary dictionaryWithObject:@"-> %{value1}@ <-" forKey:@"NSDisplayPattern"];
[textField bind:@"displayPatternValue1" toObject:arrayController withKeyPath:@"arrangedObjects.title" options:options];


And it works w/o problems for me. How did you instantiate the NSTextField (although the run-time exception seems to make clear that it really is an NSTextField instance)?

Try sending     exposedBindings to the text field and see what you get back, this is what I get:
    
fontSize, 
alignment, 
fontName, 
enabled, 
fontFamilyName, 
font, 
fontItalic, 
hidden, 
displayPatternValue1, 
textColor, 
value, 
editable, 
fontBold


----
Thank you!
You helped me catch the problem: some of the textFields affected by these bindings had a formatter attached; in that case displayPatternValue1 isn�t exposed in IB. I removed them and everything is working.

----

I tried to do the multiple bindings programmatically thing with the "enabled" binding, and it strangely doesn't work as expected.  In IB, when you set two enabled properties, (enabled and enabled2), the control is only enabled if BOTH evaluate to true.  However, when I did it programmatically, it was enabled if EITHER of the values resolved to true.  WTF?

