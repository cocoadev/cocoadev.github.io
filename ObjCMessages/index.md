---
layout: page
title: ObjCMessages
---

ObjC uses messages where most OO languages use methods. 

ObjC still has methods. Methods are concrete blocks of code. A message is an abstract 'name of a method' which, due to ObjC's dynamic nature, can invoke any number of methods.

They are written like this: 
<code>[receiver message]</code>

See http://developer.apple.com/documentation/Cocoa/Conceptual/ObjectiveC/LanguageSummary/chapter_5_section_2.html
for more info.


*A Rationale for Dynamic Method Dispatch by John Hornkvist* - http://www.toastedmarshmallow.com/Articles/DORationale.pdf

----
Objective-C objects communicate with each other by sending *messages*. The message being sent is called the selector, and the destination object is called the receiver.

Typical Objective-C messages looks like this:
    
int result = [anObject messageNameWith:argument1 andAlso:argument2];


Objective-C messages have four components: a return value, a target, a selector, and zero or more arguments.

If the object does not implement the selector, instead of immediately issuing an error, the Objective-C runtime sends another message to the object, the     -forwardInvocation: message.

    
- (void)forwardInvocation:(NSInvocation *)invocation {
  if ([anotherObject respondsToSelector:[invocation selector]])
    return [invocation invokeWithTarget:anotherObject];
  else
    [self doesNotRecognizeSelector:[invocation selector]];
}


Selector values can be computed using the     @selector() statement:
    
SEL theSelector = @selector(messageName:anotherArgument:)

returns the selector to     -messageName:anotherArgument:. By using the     theSelector a message can be sent to     anObject using Cocoa's     -performSelector::
    
// -performSelector: returns a type of kind     id, so we must cast it into a int.
int result = (int)[anObject performSelector:theSelector
                    withObject:argument1
                    withObject:argument2];


For more complex messages involving passing non Cocoa-derived objects in arguments, or larger return types than the standard size of the return type,     objc_sendMsg... or     NSInvocation must be used.
----
The Objective-C Runtime (ObjCRuntime) message mechanism works well with other programming languages.

Examples:

Code written in C/C++ can retrieve selectors and send messages by C glue code (like ObjC can call C routines). However, note that CocoaFramework provides the utility class     NSInvocation for this purpose if you are using Cocoa. 
See also: http://developer.apple.com/documentation/Cocoa/Reference/ObjCRuntimeRef/index.html

PyObjC, see http://pyobjc.sourceforge.net/doc/intro.php

See also: CocoaBridges

