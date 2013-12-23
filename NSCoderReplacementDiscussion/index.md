---
layout: page
title: NSCoderReplacementDiscussion
---




*part1 - NSCoder problems
*part2 - Proposals
*part3 - ClassCluster problems
*part4 - Backwards compatibility?
*part5 - Implementation
*part6 - Conclusion


----
part1 - NSCoder problems

What works in NSCoder? The archiving interface is fine. It is possible, even easy, to implement non-recursive archiving with NSCoder, so the     -encodeWithCoder: model can stay.

The big problem with NSCoder is that the unarchiving interface more or less mandates recursive behavior. This, in turn, is a consequence of the ClassCluster design. Recursion is mandated because an object can destroy itself and return another object while it's decoding itself.
----
part2 - Proposals

One solution is to split up decoding into two phases, an "init" phase and a "decode" phase. The "init" phase creates the object but does not decode anything. The "decode" phase decodes the object from the coder, but is not allowed to return a different object.

I propose the following protocol:
    
@protocol CCDCoding

- (void)encodeWithCCDCoder:(CCDCoder *)coder;
- (id)initWithCCDCoder:(CCDCoder *)coder;
- (void)decodeWithCCDCoder:(CCDCoder *)coder;

@end

----
part3 - ClassCluster problems

This brings up the question of what to do about class clusters. For class clusters that have a mutable version, we can implement     -initWithCCDCoder: to return an empty mutable object, and then implement     -decodeWithCCDCoder: to fill the object with the decoded values.

This has the possibly-undesirable side effect of making every class cluster decode as mutable, and it doesn't help with clusters that don't have mutable versions, like NSNumber.

One possibility would be to allow the decoding of scalar values in     -initWithCCDCoder:, but disallow the decoding of objects. Both scalars and objects would be allowed in     -decodeWithCCDCoder:(CCDCoder *)coder. This would allow CCDCoding methods to be written for NSNumber and friends, although it wouldn't fix the mutability problem.

Another possibility would be to allow the decoding of objects in     -initWithCCDCoder: when a special compatibility mode is activated. This would allow somebody to write recursive unarchiving code again, but it would at least take effort. This would solve the mutability problem and allow the writing of a wrapper method that allows NSCoding objects to work with CCDCoder, at the risk of recursive explosion.
----
part4 - Backwards compatibility?

This special compatibility mode could even simply by "don't implement     -initWithCCDCoder:, but implement     -initWithCoder: instead". The coder could be intelligent and fall back to the old coding method when the CCD version wasn't available. This would allow full backwards compatibility while allowing for an even greater possibility of recursive explosion. Maybe this is not desirable, in order to encourage people to not rely on the NSCoding methods.
----
part5 - Implementation

The implementation of a CCDCoder seems close enough to NSCoder that an existing NSCoder subclass could be adapted to serve. If enough backwards compatibility is kept, it could even remain as a subclass of NSCoder, which allows for better decoding routines that don't have the problems of NSCoder.

Encoding is exactly the same as in NSCoder.

Decoding would have to move from a recursive model to a deferred-decoding model. This is similar to how nonrecursive encoding works, and can probably borrow a lot of ideas.

----
part6 - Conclusion

It's possible to slightly modify the NSCoding API while keeping the NSCoder interface pretty much identical, and come up with an archiver that isn't forced to recursively unarchive objects. Such an archiver would be able to work with arbitrarily large and deep object graphs without crashing. it's also possible to make such an archiver backwards-compatible with NSCoding so that existing objects can be archived without changing their code, although this would bring NSCoding's problems along with it for those objects.

My proposals definitely point more towards something that is like NSCoder and is very much backwards compatible. I think it would be a good idea to be as compatible as possible, but I'm afraid that will encourage people to stick with NSCoder and not provide a clean break.

Any comments?

-- MikeAsh

----
Yes, before you do anything, I think I have a way of emulating a stack such that all we'd have to do is very gently subclass NSCoder, or maybe even a concrete NS subclass, or MAYBE even just sprout a delegate.  There are several ways of "emulating" a stack, some of which  cary context switching overhead (which is very bad).  Other possibilities include moving the stack (at the point of code/decode entry) to a queue inside the heap (which can be accomplished with relative ease and does not carry as much overhead).  Finally, there is one solution which eliminates the necessity of pushing frames onto the stack (or using longjmp), but I need to sleep on it a night and make sure it's sane.
----
Moving the stack is a nice idea, but it doesn't really remove the fundamental limit. How large do you make the stack? "Large enough" is tough to be sure of. Perhaps it would be possible to preflight the unarchiving process to get some kind of worst-case upper bound on the needed stack size, but that would probably be slow and prone to overestimation.

Emulating a stack in some other way is an interesting idea, but with similar problems. An object's -initWithCoder: method can execute arbitrary code, so you need to provide it with a full-sized stack. It's conceivable that it will store the address of a stack variable somewhere and it would be used by a child object later in the unarchiving process, so all stacks need to stay in memory at all times. You'd need something like 512kB of stack, at a bare minimum, per object to ensure safety. Not all of it would get used, and hence allocated, but it would all take up address space and inhibit the ability to decode really big graphs. (This would be much more doable with a 64-bit address space.) Are there other possibilities?

I don't really think this is the way to go, but it's an interesting idea anyway.
----
Well, the idea would be to have a queue (linked list), emulate a stack on the heap, so it would grow as needed.  Push frame would have the effect of allocating and appending a node containing the frame's data.  Sure it would run out of space eventually, but so does any call to malloc :).  The whole point would be to avoid having a real stack base/stack top.  We could return dummy values for that, so it looks like there is always one more available frame.  I agree that this is REALLY hackish, but I think it would work AND would allows us to continue with MAKeyedArchiver, thereby providing "drop in" compatability, which is nice.  But, if you're right and somewhere something in the coder mechanism is storing stack frame pointers, then this idea goes out the window.
----
I will have to agree with you: having a soft stack living in the app's heap would not be very robust and is not the way to go.  What are your suggestions?  How would we do deferred decoding?  The thing with deferred encoding is that the object being deffered EXISTS: obviously, the same does not hold true for decoding and therein lies the pickle... we could extend the interface to do a conditionalDecode, which forces the user to suspend any assumptions.  conditionalDecode returns an obejct only if it has already been decoded by a standard decode, and conditionalDecode takes a pointer to a pointer which gets properly set when the standard decode is called, or right then and there if standard decode has already been called.

*The fact that allocation is separated from initialization allows us to defer decoding. The reason this doesn't work with NSCoding is because ClassClusters break this separation of initialization and allocation. The proposed CCDCoding protocol would enforce that separation and allow deferred decoding to be accomplished without too many dirty tricks.*

I guess I'm being dense.  I still don't understand.  How do you prevent the caller from sending messages to an object that has not yet been initialized? 

*That is a good question, and one I hadn't considered. Oops. It's a big one, too! I guess you would have to document -decodeObject to return an incomplete object, and trust the caller to follow the requirements. But we're getting into annoying hackery; any object that decodes a temporary object and fiddles with it to get the final stored values would be forced to resort to ugly tricks. This might be an indication that my proposal is not as good as I thought.*

----
Well, at the end of the day the take home point here is that a general purpose archiver may not be your bag if you have a complex datastructure.  I went from 16 minutes to under 4 seconds archiving, and about the same for unarchiving by dumping data in MY format, with my own flat archiving process.  Clearly as MAKeyedArchiver demonstrates there is a LOT Apple can and should do to make their general purpose object graph encoder better, but if you know the structure of your data a priori you will always beat general purpose system hands down.

----
Well, that was fun while it lasted.  Looks like CoreData in Tigger is going to save us from all the misery we endured under Pantster, Shaguar and Peeyewma with NSCoding.

*Not exactly...it just hides it better. I bet CoreData still uses NSCoding/archives. Look at their data formats: XML file, binary file, and SQLite database file. Two out of three are currently in use by NSCoder. And by the way, what's the deal with the weird system names? I've heard of "Jagwire" and "Panthwire" but this is ridiculus. And what happened to Cheetah?*

