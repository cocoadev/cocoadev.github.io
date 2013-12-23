---
layout: page
title: DebuggingTechniquesDiscussion
---

continued from DebuggingTechniques

I felt the "parent" page was too beautiful to put a discussion on. There are many useful things there, but I cannot find answer to a question I have wondered about for a while. Is it possible to pick up messages sent to your class that you have not implemented. Say I didn't know which messages to answer if I was the NSApplication instance's delegate (In this case I know) would it be possible to set up something to catch these selectors sent to me?
EnglaBenny

----

Check out NSObject's     - (void)**forwardInvocation**:(NSInvocation *)*anInvocation* - it might be what you're looking for.

http://developer.apple.com/techpubs/macosx/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSObject.html#forwardInvocation_

----
Thanks, you put me on the right track. I am by no means less confused, however, this ends up in a lot of selectors and lowlevel cocoa stuff. It turned out that     - (void)**forwardInvocation**:(NSInvocation *)*anInvocation* did not get any calls, however overriding     - (BOOL)**respondsToSelector**:(SEL)*aSelector* did the trick to catch those delegate methods.

    
- (BOOL)respondsToSelector:(SEL)aSelector {
    NSLog(@"Call:%@", NSStringFromSelector(aSelector));
    return [super respondsToSelector:aSelector];
}

EnglaBenny

I was expecting you to override     -respondsToSelector: to always return     YES. Close enough, though, I suppose :) -- KritTer

Will it not crash upon every message that I don't respond to? Hm. This works, anyway.. --EnglaBenny

If you code     - (void)**forwardInvocation**:(NSInvocation *)*anInvocation*, you *will* respond to every message. Still, take what works. -- KritTer

----
To Engla:

I think your question was actually: how do I know which kind of messages I am supposed to respond to if I have no documentation, in the particular case of a delegate?

What happens is that if a class has a delegate, it will always ask it first if it can respond to a message before sending it, by using -respondsToSelector:. This is why if you override -forwardInvocation:, you don't get the answer to your question, because no message is actually sent so it never gets to that step. However, -forwardInvocation would be useful if a class was trying to send you messages you do not implement, and you want to handle those. The NSObject implementation gives you an exception with the information about the selector, but if you want to catch all the wrong messages, you still could tr to overwrite it like this:

    
- (BOOL)forwardInvocation:(NSInvocation *)invoc {
    NSLog(@"Call:%s", [invoc selector]);
    unsigned int length=invoc methodSignature] methodReturnLength];
    void *buffer=calloc(1, length);
    [invoc setReturnValue:buffer];
    free(buffer);
}


If the code is correct, this would return 0 or nil all the time, so it is not going to work all the time, but what can you do if you do not know the method anyway??
[[CharlesParnot

*I cleaned up the code slightly; the original was ok, but I just couldn't resist.*

----
I'm trying to do this, but I need to know when class methods are called... I tried changing to +respondsToSelector:, but it didn't worked... It's for understanding how things work in an app using SIMBL.
tiennou

