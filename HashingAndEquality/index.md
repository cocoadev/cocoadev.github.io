---
layout: page
title: HashingAndEquality
---



All objects in Cocoa have a hash value returned by **hash** and an equality method **isEqual:**. They are related. If you implement one, you have to implement the other, to make it work right in collections. Here are the rules:


* If two objects are equal, they *must* have the same hash.
* If two objects are different, they *may or may not* have the same hash.
* If two objects have different hashes, they *must not* be equal.
* If two objects have the same hash, they *may or may not* be equal.


If you implement your own **isEqual:** method, you may wish to use the sample code from http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaFundamentals/CocoaObjects/chapter_3_section_7.html, which immediately checks if the two objects have the same id (which means they are equal) and if the two objects are different classes (which means they are not equal). The sample code then calls a class-specific **isEqualToMyClass:** method, which does the real equality comparison.

When checking to see if two instances are equal, you'll probably check certain member variables and ignore others. Generate your hash from those same member variables. One popular way to do that is to XOR the member variables' hash values together and return that.

The default implementation of **isEqual:** simply checks the objects' addresses. I don't know what the default implementation of **hash** does.

----
**Mutable Objects in Collections**
----

Apple's **hash** documentation contains the following passage:

*If a mutable object is added to a collection that uses hash values to determine the object's position in the collection, the value returned by the hash method of the object must not change while the object is in the collection. Therefore, either the hash method must not rely on any of the object's internal state information or you must make sure the object's internal state information does not change while the object is in the collection. (Note that it can be difficult to know whether or not a given object is in a collection.)*

It is, in fact, impossible to know whether an object is in a collection unless you keep track of everything yourself. Also, in order to keep to the rules above, you *must* use internal state information (or else use a constant hash value, but don't do that).

But that's okay, you can ignore this passage. You won't run into any problems with NSDictionary or NSSet (the collection classes that use hash values).

NSDictionary uses the hash values of keys to look up the corresponding objects. When you give it a key A, it makes a copy B of it, so you can later change A to your heart's content. Now, if you enumerate through the keys and change them, you will screw up the dictionary. So don't do that.

NSSet uses the hash values of objects to check if they are members of the set. The documentation warns you not to alter any mutable object contained in the set. That would screw up the set. So don't do that, either.

So long as you use these classes properly, you won't run into any problems.

----

An ugly hack which would probably work here (I have used it when writing some quick Java, throw-away, code) is over-ridding the     hash method to return something totally brain-dead.

For example, I had some objects which were instances of various classes in a hierarchy stored in a Java Set.  The criteria for equality among these objects was kind of strange so the hash couldn't always give useful information (part of the problem is that some of these objects were mutable).  As a result, asking if an object was in a Set would frequently return false when it should be true.

The solution:  break the hash.  I made all of these classes return some bogus constant for the hash (5, in fact).  This way, they all get stored in the same buckets and are searched for in the same buckets.

Note that this completely kills the point of the hash since it is supposed to aid in speeding up algorithms and giving order to objects within given contexts.  As is such, this recommendation probably shouldn't be used for production code but it can help when debugging collection weirdness.

--JeffDisher

----

When creating a class whose instances have delegates, does it make sense to include an object's delegate in the hash calculation/equality evaluation?

----
I've never written a class with delegates where any equality other than object identity made sense. Can you give a more concrete example?

----

Well objects are, strictly speaking, "different" if they have different delegates. Would it make sense, then, to include a comparison of two objects' delegates in the -isEqual: implementation? e.g.
    
@implementation Atom (Equality)
- (BOOL)isEqual: (id)anObject {
	if (anObject && [anObject isKindOfClass: [Atom class]]) {
		/* should it be... */
		return  (
			[anObject atomicNumber] == [self atomicNumber] &&
			[anObject isotope] == [self isotope] &&
			anObject electronShells] isEqual: [self electronShells
		);
		/* or does this make more sense? */
		return (
			[anObject atomicNumber] == [self atomicNumber] &&
			[anObject isotope] == [self isotope] &&
			anObject electronShells] isEqual: [self electronShells &&
			anObject delegate] isEqual: [self delegate
		);
	}

	return NO;
}
@end


----
I grasp the idea that the delegate implies a difference, but I've never seen a case where an object with a delegate needs equality beyond testing if     self == other. That's why I asked for a more concrete example. Your contrived atom case is, well, not exactly realistic.

----

Why not?

----

Implementations of isEqual:  other than as     self == other are used most often for value objects:  strings, numbers arrays, dictionaries, colors, etc.  Things that you'd consider an attribute of an object rather than something that could have relationship with other objects.  If an object is more freestanding, if it's capable of having relationships with other objects (which it is if it has a delegate), it's usually going to have a simple == isEqual.  It acts more like an individual.

The closest borderline case I can think of in the frameworks is NSCell.  It is copyable, and a copy of a cell is generally treated as being equivalent to the original.  NSTableView may choose to draw with a copy of the cell it usually uses in certain circumstances, for example.  However, each cell does have relationships, such as a target for target-action, and it has other individual state like whether its pressed or not right now.  For NSCell, isEqual: is implemented as ==.

