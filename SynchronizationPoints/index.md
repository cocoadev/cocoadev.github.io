---
layout: page
title: SynchronizationPoints
---

General/SynchronizationPoints is an issue arising from running multiple threads in the same memory space simultaneously.

When a piece of memory needs to be shared between threads, a naive approach is to ignore potential problems and just have each thread access the memory when needed. This creates a *data race*, where the following can happen: 
* Thread 1 reads data from the shared memory and does something with it
* Thread 2, unknowing, reads the same data and does something with it
* Thread 1 writes modified data back to the shared memory
* Thread 2 writes new data over the top


Fortunately, we have a way of preventing threads from overwriting changed data in this way. Sounds good? Such code is called a *synchronization point*, and it has two unbeneficial effects: 
* On modern processor architectures - including the Pentium 4, and all RISC systems such as the General/PowerPC range - synchronisation is the enemy because it rends pipelining (the modern way of getting very fast processor speeds) ineffectual. Hence synchronization instructions are expensive for a single processor to execute.
* Synchronization is also the enemy of single-processor caches on multiprocessor systems. A synchronization point can happily slow down **every processor**, not just the one running the instruction.


Locks (AKA monitors) are the high-end way of programming in simple and effective synchronization points. On modern systems, it is entirely possible to rack up millions of monitor uses in a minute. That represents a massive cost to your program's speed. In all things - design carefully! Design your multithreaded code to share as little memory as feasible to achieve its aims, and try to use synchronization points as sparingly as possible. If you need to pass memory between threads, pass as much as possible at once! (It may become a juggling act between stalling a thread waiting for data and stalling the pipeline synchronizing too much.)

Remember, even the General/AtomicSQ code uses synchronization - multithreading always has a cost!

----**A brief history of time**----

To properly understand how synchronization works, and to safely write atomic-based assembler code, you need a little understanding of modern processors. Imagine your instructions coming in as a long stream to the processor (ignoring for the moment branches, etc.). As far as programmers usually care, the processor takes each instruction in turn, executes it and then moves on to the next. If that were the case, as indeed it was in days gone by, life would be much simpler.

Now imagine the processor is sitting there waiting for the slow memory bus to find and ferry some data from memory. The processor looks at the next instruction and notices that it doesn't rely on the data being fetched. "Wouldn't it be nice," thinks the processor, "if I could do that instruction now while I wait around."

That kind of optimization is the motivation behind modern processor designs. Two kinds of grief can occur: 
* A processor can execute instructions out-of-order.
* Even if the processor is forced to execute two instructions in-order, if the former is a write operation it may well not commit (get written into main memory) before the other operation completes, as it might sit around in buffers on the motherboard twiddling its thumbs.

Once you have these two issues firmly in mind, you can grasp synchronsation instructions: what they do, how they work, and what the likely cost is.

----**How bad can it be?**----

The following situations can happily be reordered by your General/PowerPC: 
* (do A; do B), where B does not depend on the outcome of A and the registers and memory involved do not overlap at all
* (do A; if X then do B), where B and A as before, and X does not depend upon the outcome of A
* (do A; if X then read L), where A does not involve memory location L, and address L does not depend upon A


Furthermore, your memory bus can happily delay the committal of any write operations after they are executed by the processor; the only stipulation is that if the same processor then reads that memory location, it should read the new value, not the old. This also does not guarantee that the value has been committed!

How bad can it be? Pretty bad. For an exact list, refer to a more worthy tome, but here is the crunch as far as memory is concerned: 
* As soon as the location that an i/o will involve is known, and no earlier operations to or from that location have yet to occur, that i/o can be performed out-of-order by the processor
* Other processors may not see writes for an arbitrary length of time.


----**Memory barriers, instruction synchronization, and memory synchronization**----

(For the rest of this page, I will adopt the naming conventions of the General/PowerPC Book E architecture.)


* An *instruction synchronisation* forces all instructions before it to complete before it will complete, and prevents any out-of-order execution of later instructions. (It also has other powers which are of  interest to kernel designers.) However, it has zero effect on the memory architecture, so storage accesses may still be pending.

* A *memory barrier* forces all memory writes prior to the barrier to complete before any memory writes afterwards can begin. This *does* affect the memory architecture - in fact, the processor itself may well care nothing for what its consequences are - and the ordering thus enforced is guaranteed to be seen by all other processors.

Memory barriers do not order memory reads, since the processor can happily carry out its speculative fetches before the barrier is up.

* A *memory synchronize* refuses to complete before every instruction preceeding it has completed, and every pending memory write has committed. (It also does those other things that kernel designers like.)

If a memory barrier acts like sending a message across the Channel to let some guys through before others, and an instruction synchronise acts like letting some people through at your end before others, a memory synchronise acts something like stopping everyone at one end and filling the tunnel with lava to flush out any stragglers. And, like the lava method, it costs quite a bit to do (though it won't actually kill anyone left behind).


----**Back to reality**----

Unfortunately, we are not yet using any Book E General/PowerPC architectures in our Macs, and two of the beasts above are represented by a mutant hybrid. Plus, a weird operation nobody seems quite sure about.


* **isync**

This is actually the instruction synchronise from above.

* **sync**

This mutant most closely represents a memory synchronization, except that it doesn't hold the whole processor up while it executes. Or, to put it another way, it's a stronger memory barrier coupled with an instruction synchronize.

* **eieio**

Enforce In-Order Execution of Input-Output. Don't ask me. Ask Old General/MacDonald. (It *looks* like eieio is a memory barrier for cacheable memory, but the documentation has always been obscure on this point.)


Just as a recap, I should mention at this point that, aside from this wonderful array of ordering primitives, our beloved General/PowerPC uses a shamelessly-RISC method of stopping processors from stepping on each other's toes while altering a single memory location: LL/SC.

Also known as Load And Reserve / Store Conditional, this pair loads the value at a memory location, then stores a new value back to it, succeeding only if the memory location has not since been altered. It can fail spuriously, cannot be nested, and dislikes wrapping write operations.

Without further ado...

----**Shameless plagiarism**----

Let us consider some of the ways Book E recommends we implement synchronisation.

To create a barrier after a LL/SC memory write that stops any later instructions at all creeping across the barrier, perform a branch ("if", to us high-level coders) that depend on the success of the memory write and then perform an instruction synchronize. This is how locks are grabbed.     
loop: r = LL(lock);
      if (r != 0) goto wait;
      if (!SC(lock, lock_value)) goto loop;
      isync; **// after the branch, this creates our barrier**
      ...
wait: ... // wait for lock to be free


If on the other hand we are importing shared storage from this pointer, we don't actually need a barrier at all, since no reads or writes to shared memory can occur before the pointer is read. The following takes a pointer off a shared location, increments the shared location, and starts using the pointer:     
loop: entry = LL(shared_ptr);
      if (!SC(shared_ptr, entry+1)) goto loop;
      entry->data = some_value;
      ...

Even though the processor may start doing things out-of-order, it will all be discarded safely and automatically if the SC fails.

To allow other processors to access shared memory, we simply need to order the write(s) that free up access to the memory after the writes to the shared memory itself. This is how locks are freed. The nearest instruction we have to a memory barrier is sync:     
sdata->last_entry = last_value;
sync;
lock = 0;


----**Take home advice**----

In order to check code for General/ThreadSafety, one needs to think about exactly how one's code could be reordered without actually violating causality. For instance, if we post a memory address on a shared pointer, we might still be writing to that memory area! Or if we reserve access to memory by setting some piece of memory to a reserved state, we might find the processor has already read data from the memory we are protecting by the time we actually set the memory to that state.

It is possible to write thread-safe code that does not need synchronizations in the most frequently run portions. An example would be memory allocation, where lists of valid objects need to be shared between threads without high overhead for reading the lists. However, one needs to examine such code very carefully to be sure one is not relying on the old notion of strict sequentiality of code execution - not for naught is it commonly advised that coders seek expert advice on making thread-safe assembler code. Hopefully, the above will give you some insight into how to think while you are looking at such code, and how to fix any problems you may find.

For more information, see: http://www-3.ibm.com/chips/techlib/techlib.nsf/techdocs/852569B20050FF778525699600682CC7/$file/booke_rm.pdf

-- General/KritTer, comments and corrections welcome
