---
layout: page
title: MessagingNil
---



Sending messages to nil is okay unless the documentation for a method states otherwise. One thing this can be used for is sending nil to your set accessor methods in your dealloc method thereby increasing readability of your code by maintaining consistent usage of accessors in your code. Of course this only works if you've written your set method to properly dispose of unused objects.

**Examples**

    - (void)dealloc
{
	[self setMyString:nil];
	[super dealloc];
}

...

- (void)setMyString:(NSString *)newString
{
	[myString autorelease];
	myString = [newString copy];
}

----

Consider the implication. [self setMyString:nil]; causes a number of interesting things to happen:


* myString is released. If myString was nil that's a [nil release] noop.
* newString is copied. If newString is nil (which it is) that's a [nil copy] which effectively returns nil.
* later when the object is freed (code left as an exercise for the reader) myString will be released again -- another [nil release] noop.


Think outside the box.

-- MikeTrent

----

Or to put it another way, setting an ivar to nil is to make it a nil object since an ivar is just an identifier for an object and not the actual object.

-- DaveHenderson

----

Eek.  The code above used to say this:

    - (void)setMyString:(NSString *)newString
{
	[myString release];
	myString = [newString copy];
}

Note the "[myString release]".  This is not safe!  Consider the case that (newString == myString).  "[myString release]" is liable to deallocate myString, which would be the same as newString.  Then we try to copy the deallocated newString.  No good.  Always use autorelease here or test if the incoming object is the old one first.

The more you know.  -=*

-- PeterJaros

----

Another way, which is the one I prefer, is to first retain newString, then release myString, then do a simple assignment myString = newString. I suppose all these ways have their merits; it's just down to personal preference in the end. --OwenYamauchi

----

From the **Universal Binary Programming Guidelines:**
http://developer.apple.com/documentation/MacOSX/Conceptual/universal_binary/universal_binary.pdf

On a Macintosh using an Intel microprocessor, Objective-C messages sent to     nil return garbage for return values that are typed as     float or     double. On a Macintosh using a PowerPC microprocessor these messages return     0.0.

*that's gonna suck.*

**It's not like it's that common. Messaging     nil is usual, and returning a     float is usual, but when would you message an unknown object to get a     float? (OK, I just thought of two or three as I was writing this) Besides,     0.0 is often a possible value (x- or y-value of coordinates, etc); something like     NAN would be much easier to differentiate. --JediKnil**

Messages to nil already return garbage for structs. Extending that to floats is not a big thing.

*It's never been safe to assume that the return from message nil was anything in particular, except when the return is an object.  I guess I may be misremembering, but I don't think messaging nil with a method returning a double will get you 0.0 on Mac OS X PPC.  I'd expect to get junk.*

----

I'm going to start a new topic about best practices for sending a message to nil.

see MessagingNilAndUniversalBinaries

----

I did some digging in the assembly code that actually implements messaging, and figured out what happens when messaging nil on PPC and Intel, 32-bit and 64-bit varieties, and in Tiger and Leopard (they're different). It's here:

http://www.contrib.andrew.cmu.edu/~ody/pages/MessagingNil.html

--OwenYamauchi

