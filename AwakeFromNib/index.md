---
layout: page
title: AwakeFromNib
---

When a nib is loaded, the nib loader allocates and initializes all objects, then hooks up all of their outlets and actions. Because of the order in which this happens, you cannot access outlets in your initializer. You can try, but they will all be nil.

After all outlets and actions are connected, the nib loader sends     awakeFromNib to every object in the nib. This is where you can access outlets to set up default values or do configuration in code. Example:

    
- (void)awakeFromNib
{
   [buttonOutlet setPeriodicDelay:2 interval:0.5];
   [textFieldOutlet setStringValue:@"Hello world!"];
}

