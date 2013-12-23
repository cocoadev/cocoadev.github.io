---
layout: page
title: HowToIterateCFDictionary
---

I guess my real question is: Is CFDictionary a wrapper for NSDictionary or is it the other way around?

Apple says that both Carbon and Cocoa are based on Core Foundation and AFAIK the source for several collection classes are part of Darwin -- so that would imply that the NS collections are actually CF collections.

But how would CF setup the proper objc_class structure?

And now for the actual question: if NS collections are wrappers for CF, how do they implement the     objectEnumerator method? cause CF does not allow for iteration of the containers.
----
NSDictionary/CFDictionaryRef are **toll-free bridged** like most of the common, primitive data structures. The implementation of objectEnumerator is most likely trivial as all that needs to happen is the creation of an NSEnumerator that understands how to enumerate a given container.

In fact     NSDictionary *myDictionary = (NSDictionary *)someCFDictionary; is quite legal.

All in all, Objective-C classes are just simple structures anyways.

----

But how would you iterate a CFDictionary? The CFDictionaryRef is an opaque structure, so the NSDictionary (assuming it is using Core Foundation APIs) would need to request an array of all keys/values.
----
Hmm, CFDictionaryGetKeysAndValues jumps to mind.

So does this...

http://developer.apple.com/documentation/CoreFoundation/Reference/CFDictionaryRef/index.html

----

Yes, that was the function I was referring to -- but if NSEnumerator uses that function then it pays a rather high abstraction penalty, since it is practically making a full copy of the NSDictionary.

Also meaning that enumerating a dictionary, set or similar would always be O(n) even when the enumeration is terminated early.
----
A copy, yes, but not a deep copy. Just the contained object pointers. A quick disassembly would reveal what the compiler is really generating. Off the top of my head, I don't think NSEnumerator is **publicly** toll-free bridged, but that is not to say the mechanism doesn't exist. I would have to check when I find the time. I don't remember anyone claiming that Core Foundation was a speed deamon. Most "reference count" API's are not.
----
Well, I found the time, just because you got me curious. I spent some time in the debugger (the new disassembly mode rocks; hit Cmd-F14 twice in the debugger) and it indeed (eventually) calls CFDictionaryGetKeysAndValues. I should point out that it is - [NSDictionary objectEnumerator] that calls this indirectly, there are a number of concrete classes that have intimate knowledge of the Core Foundation types. I will also note that the time spent in CFDictionaryGetKeysAndValues is shall we say, breif. Maybe 70 lines of assembler at most with no function calls.

Regards
----
Thanks a lot for taking the time to debug this -- I guess I should get comfortable with the debugger, as I was not aware that one could extract such detailed info from running framework code (w/o source).

I was asking the question because NSEnumerator is seriously limited (e.g. doesn't support copying, only forward iteration, no assignment during the iteration etc.), so I wanted to write a replacement, but it would seem rather inefficient, though now it is clear that the native NSEnumerator suffer from the same inefficiency.

Regarding the ~70 lines of assembler, I would assume that this is a loop making the copy, so despite the small size, it will take time proportional with the number of elements in the dictionary -- although I am more concerned about the extra storage, as iterating the entire dictionary would anyway be O(n), but wasting O(n) space pr. iteration sucks... at least from a theoretical point of view.

Although unlikely, imagine code like:
    
NSArray* values = ...;
NSDictionary* dict = ...;
NSEnumerator* outer = [values objectEnumerator];
while(id value = [outer nextObject])
{
   NSEnumerator* inner = [dict objectEnumerator];
   while(id dict_value = [inner nextObject])
   {
      if([value isEqual:dict_value])
      {
         NSLog("Dictionary has value: %@\n", value);
         break;
      }
   }
}

Let's say we have 500 values and 1000 entries in our dictionary. Then the code above will put around 4 MB in the auto release pool, which is not immediately visible from the source.


----

*only forward iteration* <- try     reverseObjectEnumerator <- oops you can't do this, but what would you gain from a reverse enumeration when values are usually hashed into a random order anyways?

your example can be done like this:

    
NSArray* values = ...;
NSDictionary* dict = ...;
NSEnumerator* outer = [values objectEnumerator];
NSArray *allValues = [dict allValues];
while(id value = [outer nextObject]) if ([allValues containsObject:value]) NSLog(@"contains value: %@", value);

----
You could if NSDictionary had a     containsObject method, which it does not.     allKeysForObject is probably the closest equivalent, but not very efficient. You could just grab     allValues up front and perform a     containsObject on that. There are a number of methods that can be used to optimize this sequence, but as this exercise is showing, a dictionary or map data structure is not really a good candidate for enumeration. If you find that your code is doing this often, you may want to consider changing your design in this regard.

I will also point out that if you use Core Foundation directly for this sequence, you bypass the autorelease pool issue.
----
Or place the dictionary values in a NSSet for O(max(n, m)) performance (assuming set construction is O(n) and set membership test is O(1), unfortunately Apple does not reveal the TimeComplexity for container methods, but I think CFSet says that membership testing is at worst O(lg N) but expected O(1), which is a bit strange, cause with hashing it could at worst be O(n), unlike e.g. a balanced binary tree, which would guarantee the O(lg N), but would also be expected O(lg N)) -- but as you hint, the exercise was not about the actual task, but merely showing that NSEnumerator can easily lead to code performing much worse than the actual source hint, since it is unknown for the user that the enumerator copies the entire dictionary (and unintuitive).
----
Here is a Core Foundation equivilant of your example that may suite your requirements and avoids the autorelease pool.
    
static __inline__ NSString* GetFirstDictionaryValueAlsoContainedInArray(CFDictionaryRef theDict, CFArrayRef theArray)
{
	NSString* result = NULL;
	CFIndex count = CFArrayGetCount(theArray);
	if (count) {
		CFIndex index;
		NSString *value;
		for (index = 0; index != count; ++index) {
			value = (NSString*) CFArrayGetValueAtIndex(theArray, index);
			if (CFDictionaryContainsValue(theDict, value)) {
				NSLog(@"Dictionary has value: %@\n", value);
				result = value;
				break;
			}
		}
	}
	return (result) ? result retain] autorelease] : nil;
}


As for design, I will note that it is not uncommon for programs to maintain two containers for a single data-set to overcome the performance characteristics of trying force a single container type to be optimal in every situation. Arrays do not make good maps and maps do not make for good arrays.
----
*As I think I have stated above, this is not what I want, it was just an example to show the flaws in the current parth of getting what I want -- I want to create an [[NSEnumerator replacement which use constant storage, can be created in constant time and for which advancement is bidirectional and also amortized O(1) in time.*
----
And as my last statement alluded to, maybe you should rethink trying to force NSDictionary/CFDictionaryRef into this mold and pick an alternate container. You would need intimate knowledge and access to the CFDictionary internals.
----
*That is no good -- I need iteration of a dictionary... and what I ask for above is really not that unreasonable or goes against any common software design philosophy -- I realize that it is not do-able with Apple's APIs, but please do not tell me that I have to change my design, I do know how to make a proper design!*
----
Well it sounds like you have still feel that a map implementation should be as efficient as an array when it comes to bi-directional linear enumeration, so you obviously know better.

You have been shown how to enumerate a NSDictionary and a CFDictionaryRef both here and in Apple's documentation.

If these containers do not suit you, then there is also std::map as well as the BSD hash and btree API's, the later can work on memory as well as files.

Regards
----
BTW,

	You should take the Big-O rhetoric down a notch. I understand it's a buzz-word in academic circles these days, but for real-world development it doesn't carry as much weight. This is coming from someone with ~20 years as a working developer.

	Let me briefly explain my reasoning. When you take thread-safety/synchronization, SIMD, Operating System architecture, and hardware architecture (CPU, memory, disk, etc) into account, you may as well throw Big-O out the window. Think about it. If that ~70 lines of assembler was using AltiVec to perform a specifically optimized hand-coded "copy" on my G4/G5 hardware, it throws your "on paper" algorithm analysis out the window by a factor of 4 (at the very least), it disrupts it even further if it was just reference counting a single pointer to an internal array while performing some other maintenance tasks, or even better, marking a VM page as private/copy-on-write and just duping the pointer.

	Now, I am not saying any of the above was the case, I just glanced at the code and didn't pay much attention to what it was doing, if any optimization was present, it was AltiVec or cache-hinting as there where no function calls, but again, I wasn't looking closely at the mnemonics.

	I guess what I am getting at is that you can't assume anything about the performance characteristics of a function/method from it's name or purpose. You will have to get your hands dirty if you want to make broad claims without having seen what it is "really" doing.

Regards

----

Seriously, the above is a load of crap -- we are talking about either storing a pointer to the first element of the dictionary in the enumerator or copy the entire dictionary into the enumerator object -- I may not have 20 years of experience, but it should not take that long to figure out that the first version is certainly always to prefer over the second one, as it require no extra storage compared to an unknown amount (at design time) of storage, and it will also need to perform a loop proportional with the dictionary in the setup compared to not executing any code at all. No matter how much help you get from SIMD instructions, it will still be slower to copy the data rather than not copy them.

Yes, we have a lot of memory we can waste today and we have a lot of processor power to utilize -- but why should we do so if we can avoid it? I have 768 MB of memory in my machine, and still it has loaded 308 MB from my swap file since boot (4 days ago), I have around a billion cycles to spend every second, but still I find that I have to wait on my machine to finish many of the tasks I ask it to execute.

And the statement that *you should take the Big-O rhetoric down a notch. I understand it's a buzz-word in academic circles these days* is equivalent to telling a meteorologist he should stop talking about the tempature in degrees and follow up with a rant about how windy weather makes it feel much colder, so his way of measurement is effectively useless, albeit a buzzword among meteorologists.

But maybe you could enlighten me, and tell me what other terminology is suitable to saying that I do not wish to copy the entire container?

P.S. Big-O is Computer Science 101 and has been so for as long as I have known about the field -- certainly not *buzzword these days*.

----

Lord,

**std::map** supports bidirectional iterators. As far as bidirectional NSEnumerator(s) are concerned, file a bug report with Apple.

I will note that std::map is currently implemented as a Red-black tree. The **std::hash_map** extension template is of course a implemented as a hash table, which (in most implementations) only support forward iteration. I guess I don't have to tell you that NSDictionary/CFDictionaryRef are implemented as hash tables.

Furthermore, given the Core Foundation calls that bypass the autorelease pool issues; I would wager a bet that the single heap allocation, and copying 1000 values out of an CFDictionaryRef and iterating through them as an array is going to be far faster than the 1000(+) Objective-C method calls made through an theoretical bidirectional NSEnumerator proxy to do achieve same thing. If you are just interested in the values, you are allocating less than 4K for the Core Foundation method, less than 8K with the keys.

----

ehm... yeah... that's great if we only have 1,000 values in the dictionary, if we only have one active iteration at a time and if we always need to iterate the full dictionary. That is quite a lot of assumptions.  I wanted to write a replacement for NSEnumerator because it lacked some features, like it does not implement NSCopying etc., the actual use case may differ a lot, so comparing the time spent copying 1,000 values with sending 1,000+ ObjC methods makes no sense to me... but then again, I am just one of those academic buzz-word craving wannabes who make theoretic assumptions about scalability...

----
Actualy, I couldn't agree more.

*With what?*

The std::hash_map implementation that I had seen that allows bidirectional iteration maintains a second container internally and marries the two up. So you have a hash table implementation that is maintaining two separate data structures. It would seem that Apple has taken a more traditional approach by basically implying that you shouldn't be using an associative container, specifically a hash-table, for linear enumeration and if you really want to do this, you will pay a price, but the NSDictionary implementation will not. I handle this in one of my projects by maintaining a separate container on top of the NSDictionary myself, quite similar to the std::hash_map I was referring to. It takes care of the fast-track keyed look-ups that the hash-table was designed for and also allows for fast, low-overhead indexing/enumeration via an appropriate container.

----

The problems I have described are really not centered around bidirectional iteration, but the cost of init, (O(n) in both space and time), which your std::hash_map iterator certainly is not (both is O(1)), and then the lack of copying.

With regard to bidirectional iteration, this is more of a general need with NSEnumerator (i.e. when used for other containers), but I wanted it for NSDictionary as well, for completeness (and I have on occasion had a problem where it would have been useful).

The problem though is a tradeoff -- either we create a sparse array (of size m, containing n elements where n <= m), and that means going to the next/previous element requires a search of at most m (empty) slots in the sparse array. Iterating the entire container would be O(m) (which is worse than O(n), but not much). But this holds for both forward and backward iteration.

To improve the TimeComplexity the container can hold a linked list, so when an element is inserted into the sparse array, it is also inserted in the linked list (vice versa for remove), that way, iteration will be done on the list (instead of the sparse array), and going to the next/previous element is always O(1) -- the price is that every element will take up a next and previous pointer (assumed to be 8 bytes).

Since CFDictionary is not made with iteration in mind, I would assume it uses the first solution (actually making init of NSEnumerator O(m) instead of O(n), although the extra operations are cheap, i.e. searching for non-empty slots).

A problem may be that the sparse array is not containing objects, but linked lists of objects (for when hashing clashes*), and to reduce overhead these lists are singly-linked (i.e. nodes have no previous pointer). So getting to the previous element of a bucket (in the sparse array) would be O(n) in the number of elements in the bucket (which is assumed to be 1, but could be n, if the hashing is really really bad).

*alternatively one can use the sparse array, e.g. if a[i] is non-empty, we simply insert at a[i+1] (repeat if non-empty). Various alternatives exist (quadratic hashing, ...) -- the advantage is the reduced memory overhead and slightly faster insertion (and most likely better cache performance), but lookup might suffer for a "close to full" hash, but only when the element is *not* found, since if a[i] is non-empty but not the element we want, we would need to test a[i+1] etc. until we find an empty slot (for the linked list approach we would do the same with the linked list, but here we do not get as many "false positives") -- though in both approaches insertion will suffer when the hash is "close to full", so it should be avoided :)
----

Whatever, We all know about proper algorithm design, it's not like these things were born yesterday. The std::hash I was referring to was using a linked list, to be specific. I am sure Apple (actually it's a NeXT implementation I believe) has good reason, besides the obvious, for implementing and having kept the NSDictionary/CFDictionaryRef (NSMutableDictionary/CFMutableDictionaryRef implied) the way they have. If you **need** to fix it for the purposes of linear enumeration, you know what to do. As another thought, a theoretical NSMap based on red-black would be trivial to implement, and as I am sure you know, you can return a subclass of NSEnumerator from your own (or overridden) - (NSEnumerator *)objectEnumerator.

----
The NSDictionary/NSEnumerator does not have the interface you are looking for, but you're forgetting the alternate interface. Cast your NSDictionary to a CFDictionary, and use a CF style function -- CFDictionaryApplyFunction comes to mind. 

That should allow itteration with O(n) time and O(1) space.

Jon H.


----

This is way off in MailingListMode, the discussion (from what I've read) isn't even about any concrete engineering issues.  The original question doesn't have anything to do with Cocoa either.

I don't know who I'm quoting here but...
1)Don't
2)Don't yet.
3)Profile before you optimize!

Alternatively, stop thinking after CS101.
DeleteMe?

