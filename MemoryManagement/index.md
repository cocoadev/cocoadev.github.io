---
layout: page
title: MemoryManagement
---



**Memory Management Overview Page**

MemoryManagement is a very central topic to programming. Many different systems for MemoryManagement have been devised, but since this is a Cocoa site, we can ignore most of them.

In Cocoa with Objective-C, MemoryManagement is handled by RetainingAndReleasing objects. Starting with Objective-C 2.0 and Mac OS X 10.5, and also when you are programming Cocoa with Java, you have the added option of using GarbageCollection.

The best thing you can do to learn memory management is to read the first two lines of RulesOfThumb. Then read them again.

=== ARC ===
Just to confuse things, in the most recent versions of Xcode (4.5 and higher), there is "ARC", or Automatic Reference Counting. For many programmers, this will vastly simplify everything, since to use it, you simply set the project to use it, and then get rid of all your retain, release, and autorelease calls. Dealloc's become mostly empty, most can go away.

Note that ARC might have an impact on performance compared to tight memory management. However, the vast majority of programs will not suffer. Release notes and documentation should be carefully studied before working around it.


**CocoaDev articles dealing with MemoryManagement:** [Topic]

Alloc+Init=New

**External articles dealing with MemoryManagement**


*http://developer.apple.com/documentation/Cocoa/Conceptual/MemoryMgmt/index.html - "Memory Management Programming Guide for Cocoa"
*http://developer.apple.com/documentation/Cocoa/Conceptual/CocoaFundamentals/CocoaObjects/chapter_3_section_5.html - "The Life Cycle of a Cocoa Object"
*http://www.stepwise.com/Articles/Technical/HoldMe.html - "Hold Me, Use Me, Free Me." By: Don Yacktman.
*http://www.stepwise.com/Articles/Technical/2001-03-11.01.html - "Very simple rules for memory management in Cocoa" By mmalcolm crawford
*http://www.stepwise.com/Articles/Technical/2002-06-11.01.html - "Accessor Methods Revisited" By mmalcolm crawford
*http://www.macdevcenter.com/pub/a/mac/2003/06/10/memory_mgmt.html - an article on the subject by RobRix.
*http://www.mulle-kybernetik.com/artikel/Optimization/opti-2.html - Allocation Basics & Foundation - an article in the series
on optimizing Objective-C.
*http://www.mac-developer-network.com/shows/podcasts/lnc/lnc010/ - Podcast on what memory is and memory management in Cocoa with Steve Scott and Uli Kusterer.
*http://www.masters-of-the-void.com/book5.htm - A chapter from a C tutorial aimed at Mac programmers that explains what memory is.
*http://mattpatenaude.com/ch3-memory-management.html - "Memory Management in Cocoa", from an upcoming eBook by the {13bold} duo


* Non OS X, but some good background information on memory management principles:


*Learning the basics of buffer overflows [http://www-106.ibm.com/developerworks/security/library/s-overflows/]
*Self-manage data buffer memory [http://www-106.ibm.com/developerworks/library/wa-memmng/?ca=dgr-lnxw07Self-manageMemory]

