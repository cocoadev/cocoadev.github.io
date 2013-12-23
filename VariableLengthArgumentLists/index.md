---
layout: page
title: VariableLengthArgumentLists
---

It is possible to use variable length argument lists (also known as varargs and stdarg) in General/ObjectiveCee.

Here is an example:

    
- (void)reportError:(General/NSString*)msg 
            explanation:(General/NSString*)explanation, ...
{
	va_list ap; /* points to each unamed arg in turn */
	General/NSString* explanation2;
	
	va_start(ap, explanation); /* make ap point to 1st unnamed arg */
	explanation2 = General/[[NSString alloc] 
                         initWithFormat:explanation 
                         arguments:ap];

	General/NSRunAlertPanel(msg, explanation2, @"OK", nil, nil);

	[explanation2 release];
	va_end(ap); /* clean up when done */
}


You can call this function with printf-style arguments:

[self reportError:@"Uh oh" explanation:@"I had %d errors", 47];

...and it will do the right thing.

However, according to Matt Watson at General/AppleComputer, it is not possible to make such a call via General/DistributedObjects.  In other words, varargs functions must be called on the same thread as the caller. 

---- 
Woah! I wouldn't go that far. I don't see why you can't call a vararg function, such as printf from a secondary thread safely. It's possible to write thread-safe programs w/o using DO. If my var arg'ed General/ObjectiveCee method is inherently thread safe, why bother with DO? I'd change that last sentence to "In other words, avoid vararg methods when using DO for IPC" -- General/MikeTrent

*OTOH, "must be called on the same thread as the caller" is not "must be called from the primary thread". I think this is a non-argument.*

*Agreed.  I think that just means that you can just call a vararg function like any other C function.  inter-thread communication doesn't support varargs.  Printf itself is thread safe, so it's fine to call multi-threded.
*

See also: General/UsingVariableNumbersOfArguments, 'stdarg' man page
----
Is there any way to make *all* of the arguments variable? As in something like this:
    
- (General/NSString *)stringFromFormatArguments ...
{	
	va_list args;
	va_start(args, _cmd);
	General/NSString *formattedString = General/[[NSString alloc] initWithFormat:self arguments:args];
	va_end(args);
	
	return [formattedString autorelease];
}

I've looked around a bit but there seems to be no way to do this... --General/JediKnil

C and the varargs implementation should support it, since you already have two implicit arguments to the function that's created,     self and     _cmd. However, the General/ObjC compiler doesn't seem to support the syntax, which is unfortunate.

*Well, it doesn't support it because, if you want to make a method that has arguments, you need to have a colon. Also, va_* only supports variable arguments if the first argument has a fixed type (see manpage); however, it should be OK to NOT have one such argument in an General/ObjectiveCee method because the runtime already supplies self and _cmd. The method would work if you did something like this, I believe:*

    
- (General/NSString*) stringFromFormatArguments:... { // note the colon


Except that, as I stated above, the General/ObjC compiler doesn't support this syntax, so you can't create such methods.
