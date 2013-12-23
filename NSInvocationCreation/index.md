---
layout: page
title: NSInvocationCreation
---

So I know how to do: [someObject performSelector:@selector(someMethod:) withObject:blah withObject:blahBlah];

But, what if I want to do more than two arguments?  performSelector does not apear to be var arg... what are my options?

----

NSInvocation is probably your only option here. -- RobRix

----

I'm having trouble getting a small implementation of HOM to work (without using a trampoline), this is as an aide for me to understand the concept. the file is located at http://catsdorule.torpedobird.com/tmp/HOM.zip . I'm tired and putting it away for the night, maybe someone will be able to shed light on a probably obvious error.

----

As mentioned in the docs for -[NSInvocation setArgument:atIndex:], the 0th and 1st arguments set the target and the selector.  To set what you think of as arguments, start at index 2.

----

For an interesting digression on this, see ObjectiveMethod by RobRix

*Editor's Note:* Content prior to 4/26/06: What turned out to be a quick question about NSTableView pasteboard operations using a method whose selector is known at compile time has been appended to the SimpleSolutionsNSTableView page.

----

I'm having a problem with creating any NSInvocation objects, no matter what sources I look at from either Apple, or other tutorials and references around the net it will always error when I try to run it.

    
- (IBAction)doAction:(id)sender {
	SEL theSelector;
	NSMethodSignature *aSignature;
	NSInvocation *anInvocation;
	
	theSelector = @selector(setIntValue:);
	aSignature = self class] instanceMethodSignatureForSelector:theSelector];
	anInvocation = [[[NSInvocation invocationWithMethodSignature:aSignature]; // Error occurs here... "EXC_BAD_ACCESS"
	[anInvocation setSelector:theSelector];
	[anInvocation setTarget:slider];
	
	NSNumber *number = [NSNumber numberWithInt:20];
	[anInvocation setArgument:&number atIndex:2];
	
	[anInvocation invoke];
}


I've tried everything I can think of, and even copied this code more or less from Apple to see if my look would be any better. No improvement. Any help would be vastly appreciated.

----

Is <aSignature> valid? If your class doesn't implement setIntValue: it will return nil, which perhaps will crash the     invocationWithMethodSignature:aSignature method. Just a thought. --GC

----

Ah, so when I request a new instance of NSMethodSignature I should always call it from the target class?

----

Err, not really. It doesn't matter where you call it FROM, but of course you need to ensure you call it for the class that implements the method of interest. It's not clear from your example code if that's the case, since you are calling     [self class] and your post doesn't say what type of object <self> is, or whether it implements     setIntValue:. Actually the code you've posted is a very long-winded way to call a method on <self>, (you could just call     [self setIntValue:20] and achieve the same thing) so I'm guessing that that's not actually your intention. But if you know what object you're going to call th einvocation on, why not just call it with setIntValue in the first place? Invocations are major overkill for that. If you intended something else, you need to explain more. --GC

Edit: I don't really understand your objective. Up top, you are using     [someObject performSelector:@selector(someSelector:arg2:) withObject:a withObject:b]. In most ordinary cases this is the same as     [someObject someMethod:a arg2:b] which is vastly simpler and extends to as many arguments as you want. So what are you trying to do? Invocations are only useful in relatively unusual circumstances, such as recording methods for later use (undo, say), forwarding messages, and sending invocations to other applications (or across threads perhaps - not sure if that's also allowed).

