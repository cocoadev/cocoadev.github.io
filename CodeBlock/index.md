---
layout: page
title: CodeBlock
---




A Block of Code. In C, such blocks are delimited by curly braces; you've seen them if you've ever written any C program at all.

However, C doesn't let you hand them around (except with function pointers, but that's messy at best). SmallTalk-80 has blocks of its own, which are delimited by square brackets, but which are full-fledged objects; you can hand them around as you please. They are used in SmallTalk to implement, among other things, HigherOrderMessaging.

It is possible to implement blocks of a sort on top of ObjC even though ObjC does not provide native support. The PortableObjectCompiler has a working implementation, and the TaskMaster paper outlines another. OCBlock is another implementation of blocks in ObjC which is implemented as an object.  -- RobRix

-- Until now. Mac OS X 10.6 �Snow Leopard� adds native support for blocks in Objective-C, using the ^ operator.

A proposal for integrating Blocks as full-fledged objects using a combination of compiler and runtime support: GCCBlocks.

FScript blocks: the F-Script framework provides an API for embedding F-Script blocks (i.e., Smalltalk-like blocks) inside Objective-C code. Since F-Script is based on the Cocoa object model, it is possible to pass Objective-C objects to F-Script blocks and get back Objective-C objects as a result of block evaluation. In addition, an F-Script block can be configured to hold references to external Objective-C objects. This article shows how to embed an F-Script block inside Objective-C (see page 2): http://www.macdevcenter.com/pub/a/mac/2002/07/12/embed_fscript.html?page=1

A try to implement the block-concept using some simple Macros can be found at MyBlocks.

----

Usually creating a method to do what would be in the block and getting its selector or NSInvocation can do what you want, here.  These approaches also get around the interesting complications that blocks add to the implementation of the VM (if we were to have it in Obj-C, we might need a complete VM) such as local variables hierarchies (aka:  *displays*), etc.

However, the way that these look in actual SmallTalk code is pretty slick, though.

--JeffDisher

