---
layout: page
title: CodingAnEfficientProxyObject
---

I've been trying to code an efficient proxy object, and thought I would put my thoughts and findings on a page on this site in case others are interested.

Using a generic proxy object Apple-style is very inefficient, I guess around a thousand times slower than a simple method call. I decided to try optimizing out this time.

*I tested the difference in speed between a proxy and a normal message, it was about 40 times slower so not as bad, but still bad - JKP*

Firstly, I realized the current method dispatch method, objc_msgSend, *has* to find a method implementation or it embarks upon a very slow process of storing all the method call information in an object (ewww). So clearly, we have to add methods to our object to get efficiency.

Thus we need a method that can redirect method calls. This is where my problems currently lie, as this is a non-trivial problem, at least on the PowerPC.

Firstly, we don't want to have to store all the information passed to us, because that takes some time. We therefore cannot have a normal function, which modifies the stack as a matter of course, nor any function calls. The stack is used to pass many arguments across, so the minute we allocate space on it we need to mess around finding the arguments passed (of which there are an unknown number), copying them out, etc. We don't want that. Function calls wipe out the other place arguments are passed: in registers.

I thus conclude that one needs to code the entire rerouting function in assembler beforehand and modify a copy slightly for every method we want redirected, in order to call the correct target method, etc.

Having drawn that conclusion, I need a strip of assembler that does everything we need. Here is a working version, annotated assembler-style:    
        lis    r15,0x0000      ; load address of proxy object
        ori    r15,r15,0x0000  ;     (self) into r15
        lis    r13,0x0000      ; load address of object pointer
        ori    r13,r13,0x0000  ;     (&self->object) into r13
        li     r14,0           ; load nil into r14
        cmp    0,r15,r3        ; if r3 == self
        beq_   normal          ; switch to normal processing
struct:           ; Process struct-returning method
        lwarx  r4,0,r13        ; load object into r4 & reserve
        cmp    0,r4,r15        ; if object is nil
        beq-   struct          ;     spin (try again)
        stwcx. r14,0.r13       ; store nil in object
        bne-   struct          ;     or spin
        isync                  ; synchronise instructions
        stw    r4,8r13         ; store object in reserved2
        b      cont
normal:           ; Process "normal" method
        lwarx  r3,0,r13        ; load object into r3 & reserve
        cmp    0,r3,r15        ; if object is nil
        beq-   normal          ;     spin
        stwcx. r14,0.r13       ; store nil in object
        bne-   normal          ;     or spin
        isync                  ; synchronise instructions
        stw    r3,8r13         ; store object in reserved2
cont:
        lis    r12,0x0000      ; load method address into r12
        mflr   r14
        ori    r12,r12,0x0000
        stw    r14,4(r13)      ; store return address in reserved1
        mtlr   r12             ; dupe r12 in link register
        blrl                   ; jump to method & return here
        lis    r13,0x0000      ; load address of object pointer
        ori    r13,r13,0x0000
        lwz    r11,4(r13)      ; reload return address
        lwz    r14,8(r13)      ; reload object
        mtlr   r11
        sync                   ; synchronise caches
        stw    r14,0(r13)      ; restore object (free lock)
        blr                    ; return


Turning this into hex is left as an exercise to the reader :) The 0x0000s need to be replaced during program execution with the correct values.

-- KritTer

----

Having struggled for over a week, I finally cracked the problem. The original needed a fair bit of work, but it can be done. **Update (20020729):** Revised code posted above.

-- KritTer

Admittedly this is all a bit more in-depth than I care to go, so I'm not sure if you address this, but.. wouldn't there be a threading problem? One of the major things I use proxies and the DO mechanism for is pushing things from child threads to the thread handling the UI. I'm sure many other uses rely on the thread safety of the object.

Will this method (or your new method) preserve that?
And under what circumstances would the object NOT find the implementation?

..--.. DaveFayram

This class is thread safe (aside from its retain/release/autorelease calls, which are currently whatever Apple supplies). You can have several threads potentially accessing it at once, and it will only allow one in at a time. It does not allow recursive locking, nor access/modify locking; it is just a very cheap, simple lock.

It will not run the code in a different thread than the caller, like DO does. That is (IIRC), standard DO proxies work by assigning one thread as the "owner" of the proxied object, and when another thread calls a method on that object, it is executed by the owner thread when it gets time to do so. This fast proxy does not work like that; it executes the method in whatever thread makes the call. It **will** stop two threads executing methods on the same object at once, and it does so transparently, making it ideal for eg objects in a shared collection. (Also provided by the proxy are methods for locking the object for a long stretch; these are trivial applications of AtomicLocking.)

In conclusion, this class does not fix the problem that UI elements can only be used by the main thread. It works on the problem of how to transparently share objects between threads without the excessive overhead of standard ObjC proxying.

-- KritTer, who appreciates he may not have just been terribly enlightening

----

I would like to chime in to the original discussion with an idea I had, and get some feedback, thoughts from other more knowlegable folks here.

I've been pondering for a while a way to implement a more efficient proxy that one that just falls back on NSInvocation.  The problems with NSInvocation forwarding are obvious (its slow as hell!) and whilst it is nice functionality to have (a way of handing unimplemented messages), the overhead seems unneccessary in situations where you already know that your target will be able to respond to the message.  I read KritTer's solution, and it is nice, but the problem is it's platform specificness - I wouldnt want to implement it in production since id need to also implement an equivilent for Intel, and whatever other archs they might choose to support ;)

So, I've been looking for a way to do this in C if possible.  I think I've come up with an idea that could work but I can see that there are lots of things that need to be thought through further, and as such I thought it would be worth adding to this page and hoping that someone might take an interest enough to offer some opinions.  I would like to add the following caveat - what follows will be viewed as most definately EVIL as far as a lot of people are concerned, but pretty much everything on this page is EVIL anyway so I'd rather not have lots of responses telling me that you shouldnt do this or that - I'm well aware that hacks like this are considered EVIL.

The basic idea I have is to do something similar to what KVO does in the depths of AppKit.  When you add an observer using KVO, it does some niftyness under-the-hood in the form of dynamically creating a new class in the runtime to act as a proxy for your observed object, and swizzling the ISA pointer of the subject so that it points to this class.  I presume that it constructs the class in the runtime such that it responds to all the messages your subject does but pointing presumably to some functions internally that do some pre and post processing work when they are called...things such as informing observers that a change is going to happen and that it did etc. 

My idea is to do the same sort of thing in my proxy class.  When you set the target of the proxy instance, you actually create a new class in the runtime specifically for your target instance.  The new class supports all the methods that you as the proxy do, and also those of the target object.  You then swizzle the ISA pointer of your proxy class, so that when a method is sent to your proxy, it is infact sent to your new class.  In your new class, the dispatch table would basically map all method IMP's to a set of functions that look somewhat like the objective-c msg sending functions.  That is there are functions that can return simple types and functions that can return structs or more complex types.  You would have to determine which one you need for each mapping when you created the class and did the swizzling.   

Now consider what would happen if you sent a message to your proxy that was destined for it's target.  Assuming you now have a function that has the correct prototype mapped to the required selector in the dispatch table, the runtime would look this up and would call the function with the usual first two arguments - target and selector.  You are now inside a function which knows about the proxy object that has been messaged, the selector it wanted to call and you have all the arguments that were supposed to be passed to the call.  For a simple proxy, you could implement this function as follows:  ask the original class if it responds to the selector that was passed, if so, then message the proxy directly.  If not, you can assume that the message was destined for the target, therefore you can construct an objc_msgSend type call for the target itself and call that.

In summary, if it worked, under this scheme you shortcut the whole NSInvocation side of things and add maybe only two function calls to the overhead of each message on top of normal messaging.  

On the negative side, there are lots of details that could be nasty to deal with.  As I previously noted, you'd need to dynamically create and manage a class for each seperate proxy instance.  You couldnt just add methods to the proxy itself because you may have multiple proxies representing different objects ... each one must look just like it's target, and only *it's* target.  this in itself probably wouldnt be that hard.  You'd need to be able to generate a unique name for the class - maybe you could use the memory address to help with this - and to know when to remove it from the runtime (but I should have thought that woudnt be too hard - when you set the target to something else, remove the old class).  

The problems I see are two-fold:  how does this ISA swizzling play with other ISA swizzling that might be going on? (IE KVO).  What would happen if you swizzled your proxy in the manner is specified above, but then someone started observing the target via KVO therefore messages to that instance are supposed to go to the KVO swizzled class.  Maybe this isnt an issue, because you are only trying to look as if you are the target - when you actually went to message the target with your objc_msgSend your lookup would actually end up messaging the swizzled KVO class.

The other issue i see is that of dynamically loaded bundles adding categories.  What if someone loads a bundle that adds a category to your target's class?  How could you  be notified of this so that you could react and add the according method to your dummy class' dispatch table?

I dont know whether either of these two issues are blockers, I'd like to hear people's thoughts on the matter.  I may well have a crack at implementing the basics over the next few days.  As I said before, I know this is EVIL, but Apple themselves use such EVIL for their own purposes - so I can't see whats stopping me doing the same.

Thoughts?

JKP

----

After some discussion in #macdev on freenode.net, it has been pointed out that there is one big problem with this idea.  That is that whilst you could catch the calls with your own custom function added to the dispatch table, you would not just be able to pass on the ... variable argument list to the objc_msgSend function.  You would actually need to pull that apart into it's seperate components and then pass each one, which means introspecting the method declaration and doing the Right Thing.  It's debatable whether you could actually get any significant speed boost over the probably highly optimised NSInvocation implementation.

Shame.

KVO can implement concerete methods for each possible case because there aren't too many.  It looks for getters and setters and there are three types of each one of those (where type can be a GPR, and FPR, or a struct...).  This is not a huge job to implement something for each of those, whereas for what I wanted to do you would have to do a LOT more work - it wouldn't really be possible in C therefore, you need to implement something more generic in asm for each platform....back to KritTer's solutions therefore!

JKP

----
Actually, you can pass along variadic args. Call va_start and va_end around a call to objc_msgSendv. See:

http://wincent.com/a/about/wincent/weblog/archives/2006/02/more_than_i_eve.php
http://developer.apple.com/documentation/Cocoa/Reference/ObjCRuntimeRef/Reference/reference.html#//apple_ref/doc/uid/TP40001418-CH1g-125829

--*boredzo*
----

----

After a day of messing around I thought it would be worth coming back here with the fruits of my labour.  The implementation of a proxy I came up with is about 10 times faster than a normal proxy using invocation forwarding though still about 8 - 10 times slower than just messaging the object in question.  In the end I didnt dynamically add any methods as although boredzo pointed out how to pass variable length arguments on to other functions, I would have needed to translate them to another type (a struct called marg_list) which is not a trivial task.  Again this would have meant enumerating the types of each argument which would negate the benefit of using this method.

What we (joint credit goes to Camillo L.) realised was that you could override forward:: and shortcut the NSInvocation thing as well, providing (we hoped) some speed increase as the need to create an NSInvocation just to do the forwarding would be negated.  In my tests the output usually looked somethimg like this: 


* 2006-03-21 19:08:23.742 FastProxy[21884] PROXY: Took 8.8663 seconds
* 2006-03-21 19:08:24.479 FastProxy[21884] FAST: Took 0.724469 seconds
*2006-03-21 19:08:24.625 FastProxy[21884] OBJECT: Took 0.137864 seconds


The test consited of sending the same message 1000000 times to an object via a normal proxy, via the fast proxy, and directly to the receiver.  As you can see the results arent  too bad, certainly worth the effort i think.

To download the code and a sample project click here -> http://www.kirkconsulting.co.uk/code/FastProxy.zip

The code I have produced is not perfect.  As it is the proxy just calls doesNotRecognizeSelector: when the target does not implement the selector - you may want to do something else such as resort to normal message forwarding...I dont know.  The other problem at the moment is that of returning structs on Intel processors.  I dont have an Intel box to test with, but I know that what I have doesnt currently work.  See the comment in the header for further info about how this could be fixed.   I would certainly appreciate it if someone with an Intel Box could take the time to fill in  the gaps in what I produced.

I hope this proves to be of use to someone.

JKP

