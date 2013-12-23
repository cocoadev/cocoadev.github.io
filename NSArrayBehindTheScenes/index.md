---
layout: page
title: NSArrayBehindTheScenes
---

I'm interested in knowing how NSArray works behind the scenes. Is it a linked list? If so, wouldn't     objectAtIndex: have to iterate through the list n times, where n is the index, meaning it's not as efficient and fast as a normal array?

----

The guts of NSArray are actually CFArray, part of CoreFoundation which is open source, so you can go and see for yourself.

----

Keep in mind that NSArray<nowiki/>'s a class cluster, which means you don't get to know exactly the way they operate behind the scenes. All NSArray*, however, are guaranteed to be TollFreeBridged with CFArrayRef (that is, they are 'seen' by Objective-C as NSArray object pointers and by CoreFoundation as CFArray references), so you can be pretty sure they are CFArray thingies deep down below.

----

CFArray comes with performance guarantees.  See CFArray.h:

    
	Computational Complexity
	The access time for a value in the array is guaranteed to be at
	worst O(lg N) for any implementation, current and future, but will
	often be O(1) (constant time). Linear search operations similarly
	have a worst case complexity of O(N*lg N), though typically the
	bounds will be tighter, and so on. Insertion or deletion operations
	will typically be linear in the number of values in the array, but
	may be O(N*lg N) clearly in the worst case in some implementations.
	There are no favored positions within the array for performance;
	that is, it is not necessarily faster to access values with low
	indices, or to insert or delete values with high indices, or
	whatever.


And no, it is not a linked list.  It's an array. ;-)

*You can implement an array interface that uses a linked list as its backing store. However, CFArray's performance guarantees would not allow this.*

CFArray would also be a really bad name for a linked list implementation.

----

So if it's an array, and not a linked list, and if it's capacity was its current size, then wouldn't insertions require a whole new block to be allocated, and all the existing items to be copied over?

*Eh, go look at the source.  That might happen, or they might be doing something a little smarter.  The guarantees allow for the possibility you mention.  "Insertion or deletion operations will typically be linear in the number of values in the array".  I.e., insertions and deletions are slow.  It's an array.*

Note that even if you implement a growable array using a big chunk of memory that gets reallocated and copied, it doesn't have to be slow. Exponential growth, where you multiply the size by some factor (typically 2) each time you run out of room, causes the time taken by copies to always be less than the time required for the insertions themselves, and so you still get an O(1) insertion time even though you have to recopy the entire array from time to time.

*That's amortized time, not worst case complexity.  But whatever. :-)*

**I assume the "worst-case O(log n)" allows them to implement a huge array as a binary tree of large chunks?**

Question is; why is there no stack or linked list in either CF or cocoa?

*Stacks exist. Just use a mutable array and add/remove from the end. You're likely to get good performance characteristics and you get stack semantics, what more could you need? As far as linked lists go, a linked list is more of an implementation detail rather than an interface, and Foundation/CF's philosophy appears to be to present an interface but not guarantee any particular implementation, in contrast to STL or Java's approach of allowing you to use specific classes in order to get a particular implementation. If you really want a linked list, writing one would be fairly trivial, and if you make it a subclass of NSArray then you could pass it to anything that takes an NSArray or CFArray.*

**Just to clarify, the STL approach does not guarantee a particular implementation, merely a particular set of worst-case behaviour guarantees on functions.**

*The TR1 additions provide a highly detailed specification of unordered_set and unordered_map including keys, hash functions, buckets and load factors -- all of which would seem to constrain the implementation to a very narrow range of possibilities.*

*Oops, my bad, I was mistaken. However, unlike Foundation/CF containers, STL's restrictions are so tight as to virtually guarantee one particular implementation, even if it's not specified. For example,     set and     map both require a comparison operator but not a hash operator, guaranteeing that it will use a tree of some kind, and not a hash table. On the other hand, I assume that     hash_set and     hash_map guarantee constant-time access (I couldn't find the behavior guarantees for these), making it a requirement to use a hash table. Foundation's collections are looser on the guarantees which makes it less clear what kind of implementation it uses.*

**    hash_set and     hash_map are not STL yet, I believe.**

*Where is the definitive reference for these things? I've been going from http://www.sgi.com/tech/stl/index.html which lists those two, but I don't know if it's actually an official spec or if it's just documenting SGI's particular implementation.*

The hashed associative containers are now called std::tr1::unordered_map and std::tr1::unordered_set and - you guessed it! - are both part of C++'s Technical Report 1 ( http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2005/n1745.pdf )

**AFAIK, the definitive reference is published in hardcopy by ANSI/ISO, available only for a fee, and written in near-incomprehensible legalese.**

