---
layout: page
title: SeLector
---



A selector is the "name" of a method used in source code as an ObjectMessage to AnObject, or the unique identifier that replaces the source code name when it is compiled.

A compiled selector is of type SEL. To obtain a compiled selector use the @selector compiler directive or the FoundationKit function NSSelectorFromString.

At first glance, it might seem like a SeLector is just a function pointer. It is actually much more dynamic than a function pointer � it's the key to messaging. A function pointer always points to the same function. A SeLector, however, works like a method name. Two different classes might have different implementations for a method (e.g.     drawRect:), but both implementations have the same SeLector.

Internally, a SeLector is implemented as pointer to a C string containing the method's name.

----

A selector is not actually required to have text (*I believe the correct term is label?*) before each colon. Try compiling the following:
    
// in header
- :x :y :z;

// in code
[obj :nil :nil :nil];

It will work fine. It's probably not a good idea to do this kind of thing because of readability, etc., but it's not illegal.

*In fact if you look at old NeXT step code you'll notice it was always (well almost always) done this way*

Apple also uses this selector-naming convention for the DOM classes that come with WebKit; they did it to preserve the function format as specified by whichever document formally defined DOM.

----

**Basics of performSelector:**

Is there a reason to use     performSelector: other than to make things more dynamic?

----

You use performSelector: when the selector isn't known at compile time. For example:

    
- (void)setTarget:t {
   target = t;
}

- (void)setAction:(SEL)a {
   action = a;
}

- (void)sendAction {
   [target performSelector:action withObject:self];
}


Here, it comes in handy because you don't know what your action is. It's determined at runtime. This kind of pattern is used for notifications, target/action setups for controls, etc.

    performSelector: doesn't do anything different from     [obj blah] in the end, it just lets you specify which selector is sent.

----

**When performSelector: is not flexible enough, e.g. with a variable argument list**

I know how to do: [someObject performSelector:@selector(someMethod:) withObject:blah withObject:blahBlah];

But, what if I want to do more than two arguments?  performSelector does not apear to be var arg... what are my options?

----

NSInvocation is probably your only option here. -- RobRix

----

Using objc_msgSend() directly is can be pretty convenient.      objc_msgSend(object, selector, object1, object2, object3).  I'd probably only use it when the arguments are all pointer sized and the return is < pointer size, since I think it's pretty clear what the compiler does in that case.  I'd be interested to hear from people with greater familiarity with the compiler (on intel and ppc?) regarding  less than pointer sized arguments and returns.

----

see also NSMethodSignature, MessageForwarding, etc.

