---
layout: page
title: StackOverflowOnUnarchive
---

I have a very, very, very (did I say "very"?) large object graph, which I need to encode.  The graph, has a set of objects all stored in a collection, and thos eobjects in turn have as instance members pointers to each other.  My probelm when archiving was that I'd get a stack overflow because the minute you start encoding the first object in the collection you end up encoding them all, without ever returning from the main call... That was easy enough to fix by calling a conditional encode from inside the nodes, and an uncodintional encode of the container class.  However I can't seem to find a matching solution when unarchiving, and so, I get a stack overflow.  I'm using NSKeyedArchiver and Unarchiver.  Without modifying my data structure or delving into the nitty gritty of the undocumented world of subclassing NSCoder what else can I do?

----

Unfortunately, this problem is inherent in NSCoder, and can't really be fixed even if you subclass [note1]. You'll have to find a way to store links between your objects outside of the coder. It's hard to say more without knowing what kind of graph you're archiving. For example, if you have a large array that contains all of your objects, and then each object has links to other objects, you could encode array indices instead of object pointers, and then hook everything up after you've finished decoding the graph.

If you don't need to solve the overall "decoding is recursive" problem but just want to see if you can fix the problem for a certain size, MAKeyedArchiver might be a good start (disclaimer: I wrote it). you have all of the code and can fiddle with it as you wish. It might use less stack per object, which could help somewhat, although it doesn't eliminate the overall problem.

**[note1] - ** This is actually not 100% true, but it's close. See recursionProblems on the MAKeyedArchiver page for details.

- MikeAsh

----
Wow, thanks for that incredibly detailed reply.  Obviously, using placeholder objects on unarchive is one way to stem the recursive calls... but that introduces a lot of overhead (linear at best for both processing and memory) I feel can be avoided.  Using alternating queues as you suggested, seems to me like the best idea, although there must be an even quicker way to dump the contents of memory onto disk and then reload from disk in an address independent way.
----
The bottom line here is that Apple's NSCoder and concerete subclasses provide a method of object graph encoding that does not scale beyond toy examples.  Coalescing NS container classes runs in polynomial time (and is not very efficient memory wise, either), and as documented on this page, the coder is recursive, leading to stack overflow even on relatively small data sets.  This kind of poor craftsmanship is not emblamatic of the Apple I know and love.  Shame, shame, shame.  I would be very interested in rolling my own: I don't, however, feel particularily motivated to work on MAKeyedArchiver: not because MikeAsh did a bad job -- he did a great job, but because NSCoder is just the wrong place to start, and as nice as it is to have a "drop in replacement", I need to it to work well, and given the lack of documentation and the bad design paradigm NSCoder uses, I'd be more interested in starting from scratch, but preserving as much of the NSCoder interface as possible.
----
The idea of an alternative, scalable coding API is intriguing. I have some ideas on this, but I'd like to collect them a bit before posting them. Perhaps we should start another page to discuss it. How about NSCoderReplacementDiscussion? Once I have my ideas down I'll post them there, or wherever we decide they should go. -- MikeAsh
----
To be honest with you, I don't know where to start.  I have this dream, where you can just litteraly dump the contents of selected memory onto disk, and then load it back in from the disk: it is more important, I feel, to have fast saves than to have fast loads.  With the exception of InterfaceBuilder, I swear I can't think of an app that loads more often than it saves.  The trick to this scheme, obviously, is dealing with memory pointers and setting them to appropriate values on load.  But, I can't think of a good (fast) way of doing that... the mechanisms for dumping and loading would be something akin to VM, maybe.
----
Well, I couldn't wait, and I posted my thoughts to NSCoderReplacementDiscussion. My proposals lean very much to the side that NSCoder generally does things right, and just needs a bit of fixing to avoid this one problem. I'd love to hear more daring ideas, though. Please read through and tell me what you think. -- MikeAsh

