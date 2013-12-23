---
layout: page
title: HowToFillAnActionMethodWithCode
---

The action method you are writing must be associated with a class.  Go to the implementation (.m) file of this class in Project Builder.  Put your action method after the # import and @implementation line and before the @end line.  Any number of action methods can be put between @implementation and @end and the order does not matter.
    

#import "MyView.h"

@implementation MyView

- (IBAction) myMethod:(id)sender
{
     // here you write your code, optionally making use of sender
}

@end



Back to HowToProgramInOSX

