---
layout: page
title: SynchronizedDirective
---



Panther adds a new Objective-C directive,     @synchronized(). See http://developer.apple.com/documentation/Cocoa/Conceptual/General/ObjectiveC/General/LanguageOverview/chapter_4_section_9.html#//apple_ref/doc/uid/20001424-BCIFAFAI

The     @synchronized() directive seems to work like Java's. It takes any General/ObjectiveC object, which acts as a mutex semaphore.

Apple's example:
    
Account *account = [Account accountFromString:[accountField stringValue]];
double deposit_amount = [amountField doubleValue];
double balance;

// Get the semaphore.
id accountSemaphore = [Account semaphore];

@synchronized(accountSemaphore) {
    // Critical code.
    balance = [account balance] + deposit_amount;
    [account setBalance:balance];
}

[balanceField setDoubleValue:balance]


----

...must...resist...urge...to...scream... ::General/KritTer vents his anger at this irksome copying of one of the less sanitary sides of Java by scrunching his face and squinting oddly::

Okay, back now. It seems Apple have merely made it easier and simpler to add an General/NSRecursiveLock to an object, and to use it. That's not so bad, I guess. Provided people don't assume that because it's easier to do, it must be easy to do. (mutter mutter race conditions mutter mutter deadlocks mutter mutter...) -- General/KritTer

----

What I'm worried about is: you can use any object as the mutex. Does this mean all objects have a built-in General/NSRecursiveLock as in Java? �General/DustinVoss

----

In the free ADC TV session he briefly mentioned the synchronize keyword and said, that unlike Java, they did not have a mutex pr. object -- but this just left me puzzled... maybe I didn't hear it correctly...

----

Does this result in any performance benefit over using General/NSLocks?  --General/OwenAnderson

----

No performance benefit, but cleaner synchronization in code that uses General/ObjC exceptions. In my tests on 10.3.4, a General/NSLock is 4 times faster than @synchronized while a General/NSRecursiveLock is 2 times faster than @synchronized. - General/SynapticPulse

----

Implementation details: see <objc/objc-sync.h>. In particular, the comment for <code>objc_sync_enter()</code> says "Allocates recursive <code>pthread_mutex</code> associated with 'obj' if needed." Presumably the mutex is deallocated on <code>objc_sync_exit()</code> if its count is 0, or on <code>-dealloc</code>... either of which is horribly ugly. On the other hand, I assume it does the Right Thing with regards to <code>@try</code> blocks. -- General/JensAyton

----

This implementation doesn't seem ugly to me. Are you are presuming the pthread_mutex is deallocated after every use, the mutex could very well be deallocated only when the semaphore is deallocated (in General/[NSObject dealloc]), or reused. (See source linked below.)

Also in my testing, it seems that if you "return" or "goto" outside the block while inside a @synchronized block the lock is relinquished (a good thing). This makes managing large blocks of synchronized code easy if you do any of this, without worrying about your locks. ~ General/TimothyHatcher

----

For implementation details see: http://darwinsource.opendarwin.org/10.3.8/objc4-237/runtime/objc-sync.m

From the GCC source this directive turns into:

    {
	id _eval_once = <sync semaphore>;
	@try {
		objc_sync_enter( _eval_once );
		/* sync code goes here */ 
	}
	@finally {
		objc_sync_exit( _eval_once );
	}
}

One limitation I recently ran into was with nested synchronized directives. You can't nest them, even if you are locking 2 different objects.

    @synchronized( obj1 ) {
	@synchronized( obj2 ) {
		/* code */
	}
}

The lock on obj1 is never released.

~ General/TimothyHatcher

*Nice catch. Did you file a bug?*

----
"The lock on obj1 is never released". Do you mean the lock remains locked, or the memory associated with it is not released ?
I'm using nested synchronized() calls (on different objects) and the app doesn't deadlock.
Did you notice the same problem in recent versions of the OS ? (ie 10.4.3 ?)

~ Nicolas

----

See also General/NSLockVsSynchronized
