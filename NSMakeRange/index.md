---
layout: page
title: NSMakeRange
---

See NSRange

NSMakeRange is defined as an inline function,

    
FOUNDATION_STATIC_INLINE NSRange NSMakeRange(unsigned int loc, unsigned int len) {
    NSRange r;
    r.location = loc;
    r.length = len;
    return r;
}

Among other things, this means that the memory for the NSRange returned by NSMakeRange() comes from the stack.  You don't have to worry about deallocating it, as the memory is made available for use after the method in which NSMakeRange() is called returns.  This also means you cannot expect a range created like this to persist after the calling function exits.  

On the other hand, this
    
//persistentRange is an instance variable
persistentRange = NSMakeRange(4,5);

is okay because the assignment operator = copies all fields when used with structures.  Same goes for functions that accept NSRange as an argument.

----

If you command + double click on any function in project builder you will be taken to the header where a particular function is defined. Here's the description for NSMakeRange:

    
FOUNDATION_STATIC_INLINE NSRange NSMakeRange(unsigned int loc, unsigned int len) {
    NSRange r;
    r.location = loc;
    r.length = len;
    return r;
}


What's happening here is no memory is really getting "allocated". If you are not familiar with the terms heap and stack, than I highly recommend checking these two memory subjects out. Basically, NSMakeRange is an inline function, which usually means no function is really being called. The compiler will simple insert the instructions "inline" with the code that contains "NSMakeRange". It's kind of like a fancy macro [http://www.greenend.org.uk/rjk/2003/03/inline.html]. This "function" saves you from having to type:

    
    range.location=someValue;
    range.length=someOtherValue;


It's really inefficient to be calling a function to setup a structure with only two int members. Telling the compiler that you would like the function to be inserted inline is a way to optimize your code for speed (not size).

