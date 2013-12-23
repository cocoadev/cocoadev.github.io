---
layout: page
title: NSControl
---

NSControl is an abstract superclass that provides three fundamental features for implementing user interface devices: drawing devices on the screen, responding to user events, and sending action messages. It works closely with NSCell.

Class Ref.: http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSControl.html

Controls & Cells: http://developer.apple.com/documentation/Cocoa/Conceptual/ControlCell/index.html 

To detect is a child of NSControl has focus (is first responder) try if ([aChildOfNScontrol currentEditor])

Also, see: NSCell

If you write an NSControl subclass without using cells, and you want to use target/actions, you have to link your Control with a NSActionCell by adding in your NSControl subclass implementation:

+ (Class) cellClass
{
  return [NSActionCell class];
}

Otherwise the target/action will return nil even when they are set in Interface Builder.

