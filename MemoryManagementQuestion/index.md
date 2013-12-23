---
layout: page
title: MemoryManagementQuestion
---

If somebody has the first edition of Hillegass's BookCocoaProgMacOSX handy, could he or she glance briefly
at the short example on p. 58 and confirm or deny that the example contains code that
should not GENERALLY be used:

1) using a factory method inside a loop

2) losing pointers right and left by repeatedly assigning the result of an instance method that returns an object

----

Nahh, no problem:

1) Factory method inside of a loop? That depends on how long your loop is going to run.

Hillegass points out in the text, "Notice that in this method, if you have 13 strings in the array, 26 autoreleased strings will be created (13 by uppercaseString and 13 by stringWithFormat:, the initial constant string is a special case and doesn't count). One of the resulting strings is returned and may be retained by the object that asked for it. The other 25 strings will be deallocated automatically at the end of the event loop."

An obvious extrapolation the reader should make based on this remark is that a 1,000,000-element array would result in 2,000,000 strings awaiting autorelease from this particular loop, probably not the best way to handle things.

2) The pointers aren't lost, they are tracked by the autorelease pool. They will all be sent final release messages at the end of the event loop, then the autorelease pool array itself will be released.

His point here is that retaining/releasing these temporary objects' pointers inside the procedure is unnecessary. autorelease handles everything, while still guaranteeing any pointers to factory objects generated inside the procedure remain valid throughout the procedure (and also throughout the procedure's calling procedure, so the caller has a chance to retain a returned pointer to any autoreleased object before it gets zapped).

This code is just to illustrate autorelease, and obviously not meant to be scaled up.

----
I agree: in most cases, autoreleasing objects is fine and is the best and more efficient way to go. You will not really slow down your app, unless you really have huge loops, at which point pure Cocoa will not be efficient, and you just go to C. Also, when speed is OK, but memory builds up fast, you can add more NSAutoreleasePools and still use autoreleased objects. I found this to be very useful when developing, when you keep adding/removing intermediate objects in your methods; if you alloc them all, you have to release them all, so every time you use another object, you should add a line at the end to release it; and vice-versa when removing an intermediate object. Look at RulesOfThumb for a discussion of the benefits that I see in using autorelease. CharlesParnot

Only alloc/init and copy create a non-released object. (and variants like allocWithZone, mutableCopy, copyWithZone). Everything else returns an autoreleased object. And any method that returns an object and that you create should follow the same rules (as recommended by Apple). CharlesParnot

----

OK. I understand. And to be fair, Hillegass makes the same remark, covering everything, on the previous page of the book.
It now makes perfect sense that no (decent) class would ever implement a method that returned (except by mistake) an object that was not autoreleased.
Except of course for alloc and copy and their relatives.

My misunderstanding arose when I read something else somewhere that explained SPECIFICALLY how it was that class (factory)
methods throughout the frameworks always return autoreleased objects, but said nothing about the rest. I didn't inquire further at the time.

And so I never used other than class methods from the frameworks in my training projects to get framework objects to use.
I was actually suspicious, if you can believe it, of all the rest. No confidence in those Apple engineers, I guess.
So I am a bit of a horse's ass, or feeling like one.

As such, I am nevertheless reluctant to join the chorus that praise the Hillegass book - to my reading, it has a kind of disjointed feel.
Maybe that's because it was written back when Apple's own documentation was kind of disjointed too, and didn't entirely overcome that hurdle.
I hope the new edition is better organized.

Well, never mind.

Let me cite my own example to see if I REALLY understand this now:

I have a mutable instance variable, say an     NSMutableArray that I allocate in the     init method of     HorsesAssClass
(and of course     release in my     dealloc method)
but I want to supply my class with a getter accessor method to return that array,
then I need to do something like

    return [ [ horsesAssArrayInstance copy ] autorelease ];

because I specifically DON'T want the senders of the getter message to be able to modify my array once they get hold of it from the getter method
-- which ability they would have (I believe) if I simply used:

    return horsesAssArrayInstance;

----

Yes!

Even better is probably to return an NSArray, so it is clear that it is non-mutable and should not be:

    return [NSArray arrayWithArray:horsesAssArrayInstance];

**Using -copy is plenty clear; the documentation explicitly states that -copy gives you a non-mutable instance if you're using a class that distinguishes between mutable and non-mutable.** *OK, oups... I did not realize this is why you have mutableCopy*

but of course, this does not illustrate very well     autorelease as it is not explicitely using it!

So maybe one could use     autorelease and also prevent modification of the members of the array (no recursivity, though):

    return [[[NSArray alloc] initWithArray:horsesAssArrayInstance copyItems:YES] autorelease];

I think     return [ [ horsesAssArrayInstance copy ] autorelease ]; is just fine in the vast majority of cases. If the members of the array are mutable, they should probably remain so in an accessor.

*The Trend over the last 20 years or so has been to design computer 
languages that enforce a state of paranoia. You're expected to program 
every module as if it were in a state of siege � In Perl culture, by 
contrast, you're expected to stay out of someone's home because you 
weren't invited in, not because there are bars on the windows.*
�� � � �from Programming Perl

I completely agree with this, and I am sorry to have proposed such a useless example (though it could be used outside an accessor in some rare cases). I love Perl for what you say too, so...

