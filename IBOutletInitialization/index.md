---
layout: page
title: IBOutletInitialization
---



If I create a class such as
    
@interface ExampleClass : NSObject
{
    IBOutlet NSTextField* name;
}

// methods...
@end

without any accessor methods, can InterfaceBuilder still set the outlet?  If so, how?

----
Yes, and look through the nifty ObjC runtime API in     /usr/include/objc for how.

