---
layout: page
title: ClassMethodQuestion
---

My class has a function called 'accumulator' which returns the value of the instance variable 'accumulator' when called. I also have another method called xSquared which also returns the value of the accumulator after squaring it. What is confusing me is should I use the return keyword to return values in the 'xSquared' method:

    -(double)xSquared
{
    accumulator=accumulator*accumulator;
    return accumulator;
}


Or should I use this code:

    -(double)xSquared
{
    accumulator=accumulator*accumulator;
    [self accumulator];
}


This code gives me a warning even though both methods work fine.

This is the code for the accumulator method:

    -(double)accumulator
{
   return accumulator;
}


Thanks - Saad R. Abbasi

----

Yes, any method that does not return void (or something which is a macro to void, such as IBAction) must have a return statement.  The warning is telling you this.

The reason why it works without is that the register used for the return value of the [self accumulator] function will not be overwritten when you return from the xSquared function.  This is not something you should depend on.

The return statement just tells the compiler that the value you are returning must be in the return register (or stack location) for its type and then return to the caller.  If there is no return then the compiler just returns to the caller at the end of the function and makes no guarantees about register usage.

--JeffDisher

----

What you need is for that second line to read     return [self accumulator];

----
Thank you for explaining this to me.

-- Saad R. Abbasi

----
I would appreciate it if someone could tell me what is wrong with this...

    

@interface generator : NSObject
{
    NSMutableData * currentBlock; // This is to store some data. It is to be initalized upon
// instantiation of this class, and retained till its gone. Its used only internally
}
-(id)init;
[...]
@end

@implementation generator
-(id)init
{
    if (self = [super init])
    {
        currentBlock = [[NSMutableData alloc] dataWithLength:16]; // <---- Problem
// This call throws a SIGTRAP and send my app to Neverneverland
    }
    return self;
}




All I want is a some memory for my data (which under C++ I would just have created with new, and under plain C with malloc). Or, in the old days, with NewHandle. What did I not understand here? Or should I just create this memblock with any of the other methods and screw Cocoa?

-- Alex

----

the problem is you are asking an instance to perform a class method. "dataWithLength" is a ClassMethod and the return value from "[NSMutableData alloc]" is an InstanceObject. You can only invoke an InstanceMethod on an InstanceObject. To correct this problem you need to ask the instance to perform an InstanceMethod. In this case the right InstanceMethod is "initWithLength:"

--zootbobbalu
----
In Objective-C, your near-equivalent to C++'s new is the [[MyClass alloc] init...] sequence (with init... being init or something like initWithLength) and just [MyClass alloc] will allocate memory similarly to C's malloc. However, to actually use an Objective-C InstanceObject, you have to use +alloc and -init.

I would highly recommend reading through the MemoryManagement page to get familiar with some of the other details of Cocoa/Objective-C memory management.

As an aside, since Objective-C is a superset of C, you still have malloc and all his friends to use in the same way that you would for procedural C code, so if you really need it in your Cocoa app, it's still available for you.
-KevinPerry

