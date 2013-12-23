---
layout: page
title: NSRangeException
---

NSRangeException is one of the pre-defined exception types.  Well, it's not actually a type per-se, but an NSString that describes the reason for an exception that is thrown with NSException.  Currently there is no official documentation on listing MethodsThrowingExceptions.

----

I have code which sets up an NSRange with an off-by-1 error: instead of setting the range location to 0, I mistakenly set it to -1, which wraps around as an large unsigned int. Then I use this range to call NSMutableString deleteCharactersInRange:. Why is an NSRangeException not being thrown for me when the location clearly lies beyond the length of the string? It seems to be thrown for a friend who reported the problem to me.

----

Exceptions are not thrown for *every* possible error. One generally hopes that Cocoa is doing as much error checking as possible, but it doesn't catch everything. For things like this, it often gets through the assertions and causes a crash. It's also possible for it to get through the assertions and end up doing nothing. There are plenty of other errors which crash and do nothing, so the fact that this one does as well shouldn't be an enormous surprise.

*Any thoughts on why it throws an exception on some machines but not others?*

----

*Raises an NSRangeException if any part of aRange lies beyond the end of the receiver�s characters.*

So if it's not beyond (i.e. -1) then it won't throw an exception. Are you sure you're executing the exact same code on these different machines?

*Yeah, it's the same code. I just tried it at work and it raised the exception successfully. I'm debugging a case where the string has 4 characters. The range is set to length=4 and location=4294967295. Um, that seems out of range.*

----

One possible explanation for the lack of an exception is that the off-by-one error mentioned by the original poster is setting the range's location to NSNotFound (which is defined as -1, or 4294967295 in the world of unsigned ints). A range where location==NSNotFound and length==0 is a valid range, but a null range: no operations using it should have any effect.

Are the ranges you're working with always the same length? I'm at work right now, nowhere near a Mac, so I can't test this... but could it be possible that a you're only getting an exception when (range.location==NSNotFound)&&(range.length!=0)? 

----

Actually, NSNotFound is actually 0x7fffffff, which is 2147483647 for both signed and unsigned 32-bit ints. It's certainly possible that NSMutableString is treating it funny because it's so huge, though.

