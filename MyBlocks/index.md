---
layout: page
title: MyBlocks
---




After turning the issue around in my head again and again and being annoyed long enough that Objc doesn't support Blocks I  tried to implement them myself - and with a bit of luck on my part got a result.

There is a preview implementation available from my homepage at http://dwt.de.vu/ so if you want to have a look, just go there.

Some comments:
This is currently a proof of concept implementation to find out how far I can push the compiler with some macros and a simple class to emulate the block concept from Smalltalk (and have easy access to closures :).

I succeeded and now have an implementation that works.

Here's some teasers of what you can do with them:

    
id aBlock = BLOCK(id, id each, return [each doSomethingThatReturnsAnID]);
id result = [aBlock value: someObject];

id aBlock = TestBlock(return [each isMaried]);
BOOL result = [aBlock test: somePerson];

id someObject = [self getAnotherObject];
id aBlock = UnaryBlock(if ([each isThisTrue]) {
					return [someObject getThis];
				} else {
					return [self getThatOtherThing];
				});
id result = [aBlock value: thatObject];


So I long for your comments and suggestions on how to improve this. :-) -- MartinHaecker

----

It's a nice idea. I played with the macro idea myself, although I never took it as far as you have.

The problem with this idea, the reason I never took it very far, is that it relies on gcc's nested functions. As you noted, nested functions don't allow for closures (referencing local variables after the return of the function). They also don't seem to be very compatible in general; I've run into problems running programs that use nested functions on some systems. I believe this is because nested functions that reference local variables have to use an executable trampoline to set everything up, which requires an executable stack. I never tested this extensively, but it seemed to die on computers that didn't have the developer tools installed; perhaps stacks are not executable without the dev tools.

Otherwise, your code is very nice and well-constructed. Be careful of cases where your macros declare symbols, such as your CLOSURE macro. Here you declare a locally-visible function named "function". Although it won't affect code outside of the macro, if the code inside the CLOSURE happens to reference an external variable called "function", then unexpected things will happen. you should rename it to something that will be difficult to reference accidentally, like _MyBlockInternalCLOSUREFunction.

Nice job, and I hope (although I'm not confident) that this approach could be practical. At the very least, it's a great illustration and implementation of this particular technique. -- MikeAsh

----

Well thanks for the compliment. :) I'l keep on testing this and see how far I can push it. -- MartinHaecker

----

I think this could be practical, I didn't know about nested functions http://gcc.gnu.org/onlinedocs/gcc/Nested-Functions.html in C until now. They are interesting, even with the limitations imposed by using nested functions, I think there's a lot of use blocks even with the limitation  of not being able to them after the function exits, or not being able to call stack variables out of scope (correction: actually you can call stack variables out of scope if the scope hasn't exited so it's just the same limitation as can't call the function after the scope exits which it was declared in, my bad I miss read the gcc docs and didn't really think about it), because the general syntax cleanliness change from this:

    
    NSEnumerator *enumerator = [testArray objectEnumerator];
    id object;
    id tempArray = [NSMutableArray arrayWithCapacity:[self count]]; 
    while (object = [enumerator nextObject]) {
        if([object intValue] < 0)
             [tempArray addObject:object];
    }


to this:

    
 id tempArray2 = [testArray select:TestBlock(return [each intValue] < 0)];


Is very awesome. I think a way to keep people from trying to use them after a method exits is to put an assertion in retain so they only can have auto-released blocks and will quickly find out what's wrong when they try to retain for instance variable storage. Returning a block from the method it was declared in would still be a problem, but the assertion would help.

I have categories for Objective-C Collection Classes for enumerating with MartinHaecker's blocks at http://www.indyjt.com/wiki/pmwiki.php/Blocks/Index

- JayTuley

Those classes are now integrated into the main-package, so you can get them while they are hot. :) -- MartinHaecker

I figured out how it is possible to do block-returns, though I think that it is a bit messy. It can be done like this:
    
- someMethod {
	__label__ blockReturn;
	id blockReturnValue = NULL;
	if (NO) blockReturn: return blockReturnValue;
	id aBlock = Closure(blockReturnValue = @"aString"; goto blockReturn;);
	[aBlock value];
	exit(-1); // not reached
}


Dunno where this could be usefull, but till then... -- MartinHaecker

