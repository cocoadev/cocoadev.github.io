---
layout: page
title: NSInvocation
---

Apple Documentation:

http://developer.apple.com/DOCUMENTATION/Cocoa/Reference/Foundation/Classes/NSInvocation_Class/Reference/Reference.html

"*An NSInvocation is an Objective-C message rendered static, an action turned into an object. NSInvocation objects are used to store and forward messages between objects and between applications, primarily by NSTimers and the distributed objects system. An NSInvocation contains all the elements of an Objective-C message: a target, a selector, arguments, and the return value. Each of these elements can be set directly, and the return value is set automatically when the NSInvocation is dispatched.

An NSInvocation can be repeatedly dispatched to different targets; its arguments can be modified between dispatch for varying results; even its selector can be changed to another with the same method signature (argument and return types). This flexibility makes NSInvocation useful for repeating messages with many arguments and variations; rather than retyping a slightly different expression for each message, you modify the NSInvocation as needed each time before dispatching it to a new target.

NSInvocation does not support invocations of methods with either variable numbers of arguments or union arguments.*"

----

Call **-setArgument:atIndex:** like this:
    
NSString *myString = @"The quick brown fox...";
[myInvocation setArgument:&myString atIndex:2];

Notice the extra ampersand.

see also SeLector, MessageForwarding, NSMethodSignature and so on.

----

If you plan to invoke some method with NSInvocation that is not of "void" return type, always specify what value you're returning in your method. For example, imagine such method that you plan to invoke using NSInvocation:

    
 - (BOOL)myMethod:(id)object {
 if ( !someCondition ) return; //error
 else 
  {
    /* ... */
  }
 return YES; //this is right
}


If someCondition is not true - your app will probably crash in a result of *EXC_BAD_ACCESS/KERN_PROTECTION_FAILURE* with **[NSInvocation setReturnValue:]** on top of stack. 
Also notice, that Cocoa uses NSInvocation for calling some of your methods in DistributedObjects and in the bridge between JavaScript <-> ObjectiveCee.

-- DenisGryzlov

----

A common way of creating NSInvocation objects that avoids setting arguments and method signatures manually is to deliberately send an unhandled message to an object. Objective-C will give you a chance to handle the message through "message forwarding" using Objective-C's inbuilt methods for this. The advantage is that Objective-C builds the NSInvocation for you, checks all your parameter types and you just save the NSInvocation it builds automatically.

The Objective-C methods required on your class to capture unhandled messages for message forwarding are:

    

- (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector
- (void)forwardInvocation:(NSInvocation *)anInvocation



Construction of a complete NSInvocation for -(id)myMethodObject:number:cString: would then look like this

    

[[NSInvocation invocationWithTarget:target invocationOut:&invocation]
    myMethodObject:someObject
    number:234
    cString:"some string"];



A detailed explanation of how this works can be found here: http://cocoawithlove.com/2008/03/construct-nsinvocation-for-any-message.html
You can download the NSInvocation category from the article here: http://cocoawithlove.googlepages.com/NSInvocationForwardedConstruction.zip

----

Apologies if this is the wrong place to ask, feel free to move it elsewhere if you think it isn't. I have a simple requirement that I think forwardInvocation should work for me, but doesn't... so I assume I'm missing something.

I have a view which is "looks into" a layered drawing. Layers are of different classes, and some might be able to respond to commands - like cut/paste for example. The view maintains the concept of an "active layer", one chosen to receive input via the key view. I want my view to forward any commands intended for the layer to the active layer, without having to implement every possible layer-oriented command itself. So I override forwardInvocation: thus:

    
- (void)				forwardInvocation:(NSInvocation*) invocation
{
	SEL aSelector = [invocation selector];
 
    if (self activeLayer] respondsToSelector:aSelector])
        [invocation invokeWithTarget:[self activeLayer;
    else
        [self doesNotRecognizeSelector:aSelector];
}


and also methodSignatureForSelector: thus:

    
- (NSMethodSignature *)	methodSignatureForSelector:(SEL) aSelector
{
	NSMethodSignature* sig;
	
	sig = [super methodSignatureForSelector:aSelector];
	
	if ( sig == nil )
		sig = self activeLayer] methodSignatureForSelector:aSelector];
		
	return sig;
}


So in one of my layer classes I have a test action method which is a standard [[IBAction. In IB I hook up a menu item using the first responder. If the method is implemented by the view, it works. But if I move it into the layer, it doesn't - the command is greyed out in the menu. My reading of the docs suggest to me that the forward invocation should appear to any outsiders as if the object itself implements the method even if in reality it's forwarding it. But it doesn't work, so what am I missing? -- GrahamCox

----

Once again, five more minutes work and I would have it solved. Once again, it turns out to be trivial. I just needed to also override respondsToSelector:

    
- (BOOL)				respondsToSelector:(SEL) aSelector
{
	return [super respondsToSelector:aSelector] || self activeLayer] respondsToSelector:aSelector];
}



Hopefully this little tidbit will be useful to somebody else trying to do something similar. If the docs do mention this, I didn't find it. -- [[GrahamCox.

----
The docs state, "NSMenu first checks to see if that object implements the item�s action method." The only way to do this is to use     respondsToSelector:, so yes, this is documented somewhat indirectly. Those three methods you overrode are the standard trifecta for doing seamless forwarding.

----
And don't forget     conformsToProtocol: ;)
-- OfriWolfus
----

