---
layout: page
title: FirstResponderTrio
---

In order to receive Key and Mouse events  an object must implement the following methods:

    

- (BOOL)acceptsFirstResponder
{
NSLog(@"Accepts First Responder");

return YES;

}

- (BOOL)becomeFirstResponder
{
NSLog(@"Become First Responder");

return YES;

}
- (BOOL)resignFirstResponder
{
NSLog(@"Resigns First Responder");
return YES;
}


By overriding     acceptsFirstResponder, you are telling other objects that your NSView subclass can handle key events and action messages. This same method returns NO inside the NSResponder class. That is why you need to override it to return YES. In object-oriented programming, a subclass inherits all methods in its superclass. The NSView class is a subclass of NSResponder so it inherits the "acceptsFirstResponder" method that returns NO. Your subclass would also inherits this method, but because you specified the "acceptsFirstResponder" method in your own class, it will use your method instead of inheriting it from NSView.

-- RyanBates

