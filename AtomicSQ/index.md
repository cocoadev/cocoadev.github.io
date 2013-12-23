---
layout: page
title: AtomicSQ
---

index: 
* part1 - The Atomic Stack/Queue
* stackSQ
* singleThreadedPopStackSQ
* singleThreadedPopQueueSQ
* starveQueueSQ
* synchronizations
* part2 - Extensions to AtomicSQ
* part3 - More on sync/isync
* licence
* appendix - Nothing new under the sun


----**part1 - The Atomic Stack/Queue**----

The Atomic Stack/Queue (SQ) is a thread-safe stack/queue implementation that does not rely on locking but on processor-specific *atomic operations* to maintain thread-safety. That means that, on the PowerPC at least, it has very rapid execution times, without the problems that locking techniques are presented by the preemptive multitasking Mac OS X gives us.

**Note that what follows is specific to the PowerPC.** On Intel and AMD x86 chips, the best atomic operation - CompareAndSwap - is not up to the task, and can only reliably replicate the two queues (and the STP queue only after a slight redesign). The stack end up with a very slight chance of screwing up totally, probably only once in a million runs but still not never. Remember this if your code needs to be portable!

The atomic SQ can only store structures (though these can of course contain ObjC object pointers) that start with a reserved void *volatile field, as in:     
struct mySQElement
    {
    void *volatile reserved; // Used by the SQ operations
    id object;
    } ;


An Atomic SQ is stored in a single <code>void *volatile</code>, passed to all the Atomic SQ functions, as in     
void *volatile mySQ;


There are four basic Atomic SQs, described below: 
* stackSQ (*)
* singleThreadedPopStackSQ
* singleThreadedPopQueueSQ (*)
* starveQueueSQ


The two types marked adhere strictly to LIFO/FIFO conventions, while the other two do not, either for speed purposes or to allow a multi-threaded pop function, respectively.

All use the same push method, AtomicSQPush, which takes the address of an SQ pointer, and the address of an element to push on the stack:     
(void)AtomicSQPush(&mySQ, &anSQElement);
 This function returns YES if the SQ was empty before the element was pushed; it executes in constant time (barring contentions).

After pushing an element on the stack, you **must not** refer to it again until you pop it off the stack. This ensures thread-safety.

Which SQ type you are using is determined by which pop function you use; never mix pop types simultaneously on the same SQ.

Here is the code for the basic push operation:

    
// Atomically push entry onto SQ
// Returns YES if the SQ was empty before entry was added
BOOL AtomicSQPush(void *volatile *SQ, void *entry)
    {
    void *previous;
    asm("lwz    r13,0(%1)  ; Load current top\n"
        "push_loop:\n\t"
        "ori    %0,r13,0   ; Copy top to previous\n\t"
        "stw    %0,0(%2)   ; Store address in new entry\n\t"
        "sync              ; Ensure all memory writes complete\n"
        "lwarx  r13,0,%1   ; Reload current top\n\t"
        "cmpw   %0,r13     ; Check it hasn't changed\n\t"
        "bne-   push_loop  ; Try again if it has\n\t"
        "stwcx. %2,0,%1    ; Swap in the new entry\n\t"
        "bne-   push_loop  ; Try again if it fails"
        : "=&r" (previous) : "r" (SQ), "r" (entry)
        : "r0", "r13", "cr0", "memory");
    return (previous == NULL);
    }


----** stackSQ**----

The stack maintains thread-safety with a constant-time (barring contentions) pop function without locking. It guarantees a LIFO (last-in-first-out) ordering of insertions and retrievals.

Every stack pop represents a synchronization point.

    
// Atomically pop an entry off the stack SQ
// Returns NULL if the stack was already empty
void *AtomicStackPop(void *volatile *SQ)
    {
    void *entry;
    asm("ASP_loop:\n\t"
        "lwarx  %0,0,%1     ; Load current top\n\t"
        "cmpw   %0,%2       ; Check it isn't NULL\n\t"
        "beq-   ASP_end     ; If it is, stop\n\t"
        "lwz    r13,0,(%0)  ; Load next entry\n\t"
        "stcwx. r13,0,%1    ; Swap it into SQ\n\t"
        "bne-   ASP_loop    ; Try again if it fails\n"
        "ASP_end:\n\t"
        "isync              ; Prevent later memory loads starting early"
        : "=&r" (entry) : "r" (SQ) : "r" (NULL)
        : "r0", "r13", "cr0", "memory");
    return entry;
    }


----**singleThreadedPopStackSQ**----

The single-threaded-pop (STP) stack has a lock-free aggregate-constant-time pop function that, while thread-safe wrt the standard push, must only be used by one thread at once on the same SQ. (Multiple pushes can be occurring simultaneously on other threads, of course.)

It gives a semi-LIFO ordering of insertions and retrievals, in that the thread retrieving data will temporarily ignore newer items placed on the stack while it processes the stack already there, but will process the retrieved items as a stack.

The function represents an optimization over the stricter stack above because it can operate with fewer synchronization points; these synchronizations occur essentially unpredictably. It is also an optimization over the STP queue below because retrieval time is constant (barring contentions), not aggregate-constant.

To operate, the STP stack needs a single reserved void* per SQ, referenced only by the single thread that will perform the pop operation. (This pointer is used to store the stack locally instead of returning to the SQ pointer each time.) The address of this local stack (lS) should be passed into the pop function each time it is used.

    
// Pop an entry off the STP stack SQ
// Needs a void* address (lS) for optimization
// Returns NULL if the stack was already empty
void *AtomicSTPStackPop(void *volatile *SQ,
                        void **lS)
    {
    // Use a thread-local stack to cache entries
    // Minimizes synchronization points
    void *entry;

    // Do we still have entries on lS?
    if (*lS == NULL)
        {   // Pop the stack off SQ onto lS
        asm volatile ("STPSloop:\t"
                      "lwarx    %0,0,%1  ; Load current top\n\t"
                      "stwcx.   %2,0,%1  ; Swap in NULL\n\t"
                      "bne-     STPSloop ; Try again if it fails\n\t"
                      "isync"
                      : "=&r" (*lS)
                      : "r" (SQ), "r" (NULL)
                      : "cr0", "memory");
        }

    // Pop the next entry off lS
    entry = *lS;
    if (*lS != NULL) *lS = *(void**)(*lS);
    return entry;
    }


Note: this function was added to the AtomicSQ page on October 18th. It has not been tested or compiled.

----**singleThreadedPopQueueSQ**----

The single-threaded-pop (STP) queue has a lock-free aggregate-constant-time pop function that, while thread-safe wrt the standard push, must only be used by one thread at once on the same SQ. (Multiple pushes can be occurring simultaneously on other threads, of course.) It guarantees a FIFO (first-in-first-out) ordering of insertions and retrievals.

The function synchronizes essentially unpredictably.

To operate, the STP queue needs a single reserved void* per SQ, referenced only by the single thread that will perform the pop operation. (This pointer is used to store the queue reversed locally instead of returning to the SQ pointer each time.) The address of this local stack (lS) should be passed into the pop function each time it is used.

    
// Atomically pop an entry off the STP queue SQ
// Returns NULL if the queue was already empty
// Pop an entry off the STP queue SQ
// Needs a void* address (lS) for optimization
// Returns NULL if the stack was already empty
void *AtomicSTPQueuePop(void *volatile *SQ,
                        void **lS)
    {
    // Use a thread-local stack (lS) to cache entries
    // Minimizes synchronization points
    // Also makes retrieval time aggregate-constant, not linear
    void *entry;

    // Do we still entries on lS?
    if (*lS == NULL)
        {   // Pop the queue off SQ
        void *queue, *temp;
        asm volatile ("STPQloop:\t"
                      "lwarx    %0,0,%1  ; Load current top\n\t"
                      "stwcx.   %2,0,%1  ; Swap in NULL\n\t"
                      "bne-     STPQloop ; Try again if it fails\n\t"
                      "isync"
                      : "=&r" (queue)
                      : "r" (SQ), "r" (NULL)
                      : "cr0", "memory");
        while (queue != NULL)
            {   // Reverse it onto lS
            temp = *lS;
            *lS = queue;
            queue = *(void**)queue;
            *(void**)(*lS) = temp;
            }
        }

    // Pop the next entry off lS
    entry = *lS;
    if (*lS != NULL) *lS = *(void**)(*lS);
    return entry;
    }


Note: this function was optimized to use an aggregate constant time algorithm on October 18th. It has since been tested.

----**starveQueueSQ**----

The starve queue, the least ideal of the basic Atomic SQs, has a thread-safe pop operation that starves other pop threads of data during its execution, and will allow pushed data to "fall" to the front of the queue during the starve. If no contentions occur, a FIFO ordering of insertion and retrieval is guaranteed. The pop operation takes linear time, again making this method unsuitable for large lists.

Every starve queue pop represents a synchronization point.

    
// Atomically pop an entry off the starve queue SQ
// Returns NULL if the queue was already empty
// A NULL return may also indicate a starve lock by another thread
void *AtomicStarveQueuePop(void *volatile *SQ)
    {
    void *queue, *entry, *last;
    asm("StQ_loop1:\n\t"
        "lwarx  %0,0,%1    ; Load current top\n\t"
        "stwcx. %2,0,%1    ; Swap NULL into SQ\n\t"
        "bne-   StQ_loop1  ; Try again if it fails\n"
        "isync             ; Prevent later memory loads starting early"
        : "=&r" (queue) : "r" (SQ), "r" (NULL)
        : "r0, "cr0", "memory");
    // Return NULL if the queue was empty
    if (queue == NULL) return NULL;
    // Find and remove the last entry of queue
    // Rely on starve lock to allow single-threaded code
    last = NULL;
    for (entry = queue; *(void**)entry != NULL; entr = *(void**)entry)
        last = entry;  // This takes linear time
    if (last != NULL)
        {  // Need to swap queue back onto SQ
        asm("sync              ; Ensure all memory writes complete\n"
            "lwz    r13,0(%1)  ; Load current entry\n"
            "StQ_loop2:\n\t"
            "ori    r14,r13,0  ; Copy r13 to r14\n\t"
            "stw    r14,0(%2)  ; Store r14 at end of queue\n\t"
            "lwarx  r13,0,%0   ; Reload current entry\n\t"
            "cmpw   r14,r13    ; Check it hasn't changed\n\t"
            "bne-   StQ_loop2  ; Try again if it has\n\t"
            "stwcx. %1,0,%0    ; Swap in the new entry\n\t"
            "bne-   StQ_loop2  ; Try again if it fails"
            : : "r" (SQ), "r" (queue), "r" (last), "r" (NULL)
            : "r0", "r13", "r14", "cr0", "memory");
        }
    return entry;
    }


----**synchronizations**----

The "essentially random" synchronization in the STP functions will occur less frequently if the SQ is very full when it is accessed; thus threads using the STP functions will tend to become faster when they fall behind on processing their SQ. See SynchronizationPoints for a definition of, and the costs associated with, synchronization in code.

----**part2 - Extensions to AtomicSQ**----

The code described above can be fitted into other fast, thread-safe code. For an example, see the ProducersAndConsumerModel page, which describes how to extend the single-threaded-pop atomic queue code to sleep the thread doing the popping when the queue is empty.

----**part3 - More on sync/isync**----

As of September 10th, 2002, the above code contains correct synchronisation barriers, using sync and isync [20030120 - sync correctly positioned in insert operation]. These memory barriers are perhaps the most expensive part of any atomic operation, because they temporarily disable the pipelining parts of the PowerPC chip. The exact operation of these commands is tied up with processor theory; see SynchronizationPoints for a look at how and why they work.

These operations are also used in NSLock code, so we can still claim the AtomicSQ is more efficient than the same code using locking instead of atomics. However, it is always more efficient to design your code to keep the shared memory at a minimum. The AtomicSQ is as efficient as it is because it only shares a single word of memory. Sharing no memory at all is even better.

It should be correct to remove use of isync in the code, because memory popped off the atomic stack *cannot* be speculatively fetched before it is safe to do so. However, once you do that, you need to bear in mind exactly **why** this operation works, and avoid any carelessness that would break our guarantees. (For example, if you used an atomic stack to pass around "access priveledges" to some other shared memory, and merely tested whether you has access or not with an "if", speculative reads could leak across the memory barrier. Ewww.)

For this reason, I have left the "isync" in the code above, because with it in the memory barrier is strictly enforced by the processor. It may be slower, but only on one processor, and in the name of idiot-proofing.

----**licence**----

All the code on this page is under the following licence:

    
// Copyright (c) 2002-2003 Chris Purcell.
//
// Permission is hereby granted, free of charge, to any person obtaining a
// copy of this software and associated documentation files (the "Software"),
// to deal in the Software without restriction, including without limitation
// the rights to use, copy, modify, merge, publish, distribute, sublicense,
// and/or sell copies of the Software, and to permit persons to whom the
// Software is furnished to do so, subject to the following conditions:
//
// The above copyright notice and this permission notice shall be included
// in all copies or substantial portions of the Software.
//
// THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
// IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
// FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL
// THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
// LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING
// FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER
// DEALINGS IN THE SOFTWARE.


Please correct mistakes, but be aware this licence still applies. If you feel this is too restrictive, tell me.

----**appendix - Nothing new under the sun**----

Although I designed and coded all this by myself, including modifications I have not yet made, it turns out that none of this is new ideas, and indeed Apple has used them for some time. Check out http://redshed.net/macHack/99/atomicity.html for a good page which describes, basically, exactly what I have coded on this page.

Nevertheless, I will leave this code here - I have not found actual code anywhere on the web, only the general ideas - so others can use it. (And, yes, it's still under the same licence.)

-- KritTer

----

The code to the paper referred to above can be found at http://redshed.net/atomicity/index.html

It's currently CodeWarrior-only, though I may bring it to ProjectBuilder/GcC one day...

--JonathanWolfRentzsch

