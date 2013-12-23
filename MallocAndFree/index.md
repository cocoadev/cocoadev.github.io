---
layout: page
title: MallocAndFree
---

I know things about many memory management systems but I am little fuzzy on C's...I am working on the interesting bit of FSObject right now and I think the main logic is okay, but I have no idea at all about memory issues.  If I malloc(3)ate a new objc_method struct, objc_method_list struct, and calloc(3)ate the array of objc_method pointers am I responsible for freeing them? I want them to stay alive as long as the process does so my options are thus:

A) don't worry about it... unix will reclaim all the memory the process grabbed when tearing down the process... (????)

B) don't worry about it... the ObjCRuntime when it starts shutting itself down at the end of your process will deallocate the memory so you don't need to (and shouldn't) free(3) it. (???)

C) I should put a snippet of code between NSRunApplicationLoop() and return in my main function that goes through all of the categories I created on FSObject and free them, and all their subobjects that I personally allocated. (???)

D) I have missed it completely... I either don't need to malloc etc. to make new instances of the structures, or calls to free go somewhere else (???)

well, anybody out there with malloc/free experience or even better, ObjCRuntime hacking?

----

In the normal course of things you are responsible for freeing everything you malloc or calloc. Try to take care of it if at all possible. Unix usually frees memory when a process dies, but there are cases (mult-threaded / shared memory) where  not freeing the memory could create a memory leak that outlives the process. 

----

If you malloc something that should live for the life of the process, don't worry about freeing it. The OS will reclaim it.

----

well if the special cases where it won't be reclaimed are threads, than it's okay, because I CoProcess instead...

----

When a process is terminated, all of its threads are terminated as well, so there's no way that multithreading could create a leak that outlives your process.

---- 

I think the only case where memory is not reclaimed by the OS when a process terminates is if you use BSD calls to share memory between processes, in which case some of the memory won't be reclaimed until all of those processes terminate. 

In other words, unless you're using mmap(), shmget(), or other Unix-shared memory calls directly, you don't have to worry about it.

