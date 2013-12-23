---
layout: page
title: HowToDeclareAnActionMethod
---

An action method can be declared either by using the Interface Builder (see HowToCreateAnActionMethod), or by using Project Builder.

To declare an action method using Project Builder, go to the header file (.h) for the class in question.  Place the action method declaration between the close bracket (}) and the @end line.

    
#import <Cocoa/Cocoa.h>

@interface MyClass : MyClassSuperclass
{

    put variables here;
    
}

**- (IBAction) myActionMethod:(id)sender;**

@end



Go back to HowToProgramInOSX

----
**Edit 24 November 2003** changed method declaration from <code>-(void)myActionMethod;</code> to <code>-(IBAction) myActionMethod:(id)sender;</code>

IBAction is #define'd as void (and IBOutlet is #define'd as nothing) in AppKit/NSNibDeclarations.h but IB will look for IBAction (or a (id)sender parameter) in header files dragged into it and add actions for those methods.

