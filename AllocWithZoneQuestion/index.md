---
layout: page
title: AllocWithZoneQuestion
---

So what's the deal with allocWithZone:  Should I use it as much as possible, meaning all my member variables should use their owning classes' zone.  What discretionary steps should I use when determining whether to use alloc or allocWithZone: [self zone]

- FranciscoTolmasky

----

First, check out NSZone

Basically, there is a performance issue related to virtual memory and heap management that is closely tied to paging and memory allocation/deallocation. Allocating memory with zones is an attempt to localize memory usage. 

Memory management is a predictive battle. The best strategy depends on the usage patterns for a given application. For example, a document-based application implies a predetermined allocation pattern. Objects supporting a given document are usually active while the window displaying the document is responding to user input. Objects residing in physical memory are orders of magnitude more responsive than objects stored on disk. Keeping objects grouped together based on document association is a logical thing to do. 

The advantage of doing this comes from the details of how virtual memory works. Currently virtual memory on OS X swaps data from physical memory to disk in 4096 byte chunks called pages (just type     vm_stat in Terminal to see what the current page size is). A single page of memory is able to store multiple objects. NSZone will group objects together so related objects reside within a page. Using zones also improves the probability that related objects spread across multiple pages will be grouped together when temporarily stored on disk. 

Another advantage of using zones comes from the details of how dynamic memory allocation/deallocation works. Technically, you are already allocating with a zone even if you don�t use the allocWithZone: method. Class objects share a default zone when factory methods are used and when objects are allocated explicitly with �alloc�. The default zone is a clean slate when an application first launches. A request for memory is a trivial and efficient operation when the default zone is in a contiguous state. As objects are released the default zone becomes fragmented and more overhead is required to allocate new objects. Using zones sensibly can reduce this overhead by providing localized �clean slates� for allocation patterns that require objects to be grouped together and created with little delay. 

--zootbobbalu

----

NSZone has been castrated, and is pretty much just a legacy no-op, which is a shame, since it was handy for doing bulk frees of similar objects (like tree nodes).

----
Is that last comment (regarding NSZone being a legacy no-op) accurate?

*After having done some research on 10.3, I don't believe it's a no-op, but it's mostly castrated, yes. It's lost some of its good functionality, and as far as I can see, the only really good thing left about it is that there's the potential for replacing it with your own zone scheme casted to     NSZone *.*

