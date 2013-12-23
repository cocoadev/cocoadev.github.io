---
layout: page
title: AutoRelease
---



When RetainingAndReleasing objects, if you don't know how long you are going to need an object reference (for instance, when you     return an object),
you could be faced with something of a conundrum.

Obviously, you won't be able to send it the     release message within the method that creates it, or it won't remain for use by other methods. But if you 
don't send it a     release message, the method that creates it will cause MemoryLeaks. For example, the following has a MemoryLeak:

<pre>
// leaks - don't do this
- (NSAttributedString *)generateTime:(NSString *)contents
{
         NSAttributedString *createdString = [[NSAttributedString alloc] initWithString:contents];
         [createdString applyFontTraits:NSBoldFontMask range:NSMakeRange(0, [contents length])];
         return createdString;
}
</pre>

The solution presented by the FoundationKit is the AutoReleasePool. By sending an object an     autorelease message,
it is added to the local AutoReleasePool, and you no longer have to worry about it, because when the AutoReleasePool is destroyed
(as happens in the course of event processing by the system) the object will receive a     release message, its RetainCount will be decremented,
and the GarbageCollection system will destroy the object if the RetainCount is zero.

Naturally, the method that receives an     autoreleased object needs to     retain it if extended use of it is required. An amplified discussion occurs in
QuickAutoreleaseQuestion in RetiredDiscussions.

----

An example:

<pre>
- (NSAttributedString *)generateTime:(NSString *)contents
{
         NSAttributedString *createdString = [[NSAttributedString alloc] initWithString:contents];
         [createdString applyFontTraits:NSBoldFontMask range:NSMakeRange(0, [contents length])];
         return [createdString autorelease];
}
</pre>

----

Generally speaking, objects returned from methods other than     copy or     alloc and their variants should be considered to be autoreleased.

Here is a debugging example that illustrates the pitfalls of using autoreleased objects, and aimed at the unwary newcomer;
(note that this example also debugs a wide-open memory leak derived from a misdirected use of     alloc):

----

A log statement in my program causes a crash:

In MyDocument.h, I have the declaration:  NSArray *anArray;

In MyDocument.m, I have (in part):

<pre>
- (IBAction)openFile:(id)sender//I click a button
{  
   . . .
NSString *contentsOfFile = [[NSString alloc] initWithContentsOfFile:myPath];

	anArray =[[NSArray alloc] init];

    anArray = [contentsOfFile componentsSeparatedByString:@"\xD"];//CR

		NSLog(@"anArray = %@",anArray);//correctly list the 139 strings in anArray
}

- (IBAction)extractData:(id)sender//I click another button
{
	NSLog(@"anArray = %@",anArray);        //crashes with the message: program "has exited due to signal 5 (SIGTRAP)."
}
</pre>
	Why does this crash occur?  There should be no scope problem since anArray has been declared outside either Action.  Just to test, if I make the declaration "NSArray *testArray; in MyDocument.h, then in the first Action above I write:
<pre>
testArray = [[NSArray alloc] initWithObjects:@"hello",@"goodbye",nil];
NSLog(@"testArray = %@", testArray ); //works fine
</pre>
	and in the second Action, I write
<pre>
NSLog(@"testArray = %@", testArray ); /also works fine--no crash
</pre>

----

**The     componentsSeparatedByString: method returns an autoreleased array.** If you need to use this array later, you have to retain it. Remove the     anArray =[[NSArray alloc] init] (it's a MemoryLeak) and change the next line to         anArray = contentsOfFile componentsSeparatedByString:@"\xD"] retain];//CR. You could also replace the @"\xD" with @"\r".

The reason your test worked was you created the test array with alloc/init which returns an object with a [[RetainCount of 1. To avoid another leak, you'd have to     release it in your     dealloc method, or some other suitable place.

