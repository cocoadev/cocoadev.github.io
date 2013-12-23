---
layout: page
title: ObjectiveSmalltalk
---



Objective Smalltalk is a project by MarcelWeiher to integrate Smalltalk directly with the Objective-C runtine.

The following are some comments by Marcel found on Apple's Objective-C mailing list ...

Anyway, my (spare) energy is currently focused on Objective- Smalltalk, which is coming along very nicely. This is 100% integrated with the Objective-C runtime, as it really only represents an alternate (simpler) syntax for sending Objective-C messages to Objective-C objects and working with variables (local, instance, customized). In fact, it achieves my long-term goal for CocoaSqueak, the Objective-C bridge and the Objective-C code generator: a Smalltalk where there really isn't any distinction between the VM and the objects sitting "on top" of the VM. Put another way: an object system whose VM is implemented by sending (standard) messages to objects that are visible from, and could be implemented in, the system.

Method implementations are normal Objective-C objects, so you can have different classes of methods. For example, instead of generating code for accessors (wether by tool or by C macro), you just create a new instance of the accessor method class(es), bound to a particular instance variable and add it to the class in question. I found that bit very satisfying :-)

The interpreted environment doesn't currently have any primitive types, just objects. It does, however have literal syntax for creating these kinds of objects and will automatically convert to and from primitives when sending messages and invoking methods. The syntax allows for optional type declarations that are currently used in method header declarations and can/could be used in a compiled environment to directly emit code for primitives. The syntax uses the angle-brackets-style from StrongTalk and Smallinterfaces.

