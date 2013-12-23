---
layout: page
title: CocoaBindingsAndMultiThreading
---



I cannot for the life of me get Cocoa bindings and threading to work at the same time reliably... I've tried NSLock to no avail... perhaps I am doing something wrong, but I really don't think so as I locked the entire section where data is modified, so the Ui shouldn't be reading or writing to it while that is happening, right? Yet it crashes very happily. Now that I write this, I have an idea as to what it might be... But I still wonder, can anyone provide the proper way for working with the two in combination?

----

Don't try to access the AppKit from another thread.

*It's probably easiest to make your main thread merely handle the GUI, and use NSObject's     -performSelectorOnMainThread:withObject:waitUntilDone: to allow other threads to read/update bindings. Opinions?*

----

If you're updating a key value and the update is progated across multiple threads you need to lock the accessor and the setter:

    
// accessor method
id value
[_lock lock];
value = _value // if the address of the value changes or if it's a scalar, otherwise, value = _value copy] autorelease];
[_lock unlock];
return value;

// value moding code
[self willChangeValueForKey:@"value"];
[_lock lock];
[_value someMutatingMethod]
[_lock unlock];
[self didChangeValueForKey:@"value"];


Werx fur mee!

----

Note that Cocoa bindings (more specifically, [[KeyValueCoding) uses notifications internally, and notification callbacks are run on the same thread that posted the notification.  This means, for example, that if you have a NSTextField whose string contents are bound to an NSString ivar in an object, that you should update the NSString ivar in the main thread.  If you update the NSString in a background thread, the NSTextField will also be redrawn in the background thread, causing some weird redraw problems and possibly causing hangs or crashes.  You might find the following code snippet useful if you're doing heavy KVC work in background threads: it's a category on NSObject.

    
#include <pthread.h>

@implementation NSObject (KVCThreading)

- (void) performSelectorOnMainThread:(SEL)aSelector withObject:(id)argument1 withObject:(id)argument2 waitUntilDone:(BOOL)wait
{
    NSAutoreleasePool* pool = [[NSAutoreleasePool alloc] init];
    
    NSInvocation* invocation = [NSInvocation invocationWithMethodSignature:[self methodSignatureForSelector:aSelector]];
    
    [invocation setSelector:aSelector];
    [invocation setArgument:&argument1 atIndex:2];
    [invocation setArgument:&argument2 atIndex:3];
    [invocation retainArguments];
    
    [self performSelectorOnMainThread:@selector(handleInvocation:) withObject:invocation waitUntilDone:wait];
    
    [pool release];
}

- (void) handleInvocation:(NSInvocation *)anInvocation
{
    [anInvocation invokeWithTarget:self];
}

- (void) setValueOnMainThread:(id)value forKey:(NSString*)key
{
    // pthread_main_np() appears to be undocumented on the Mac OS X manpages:
    // it returns 1 if it's called from the main thread.
    switch(pthread_main_np())
    {
        case 1:
            [self setValue:value forKey:key];
            break;
        default:
            [self performSelectorOnMainThread:@selector(setValue:forKey:) withObject:value withObject:key waitUntilDone:NO];
            break;
    }
}

@end

