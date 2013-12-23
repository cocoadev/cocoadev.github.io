---
layout: page
title: NSObjectProtocol
---

NSObject is also the name of a protocol that the real NSObject implements, and thus all classes that inherit from NSObject do. Its most important functions is providing the MemoryManagement messages     release,     retain, and     retainCount, as well as the     self constant and the message     isProxy that returns TRUE if the class is a root class (i.e. it does not inherit from NSObject). - PietroGagliardi

Some classes which do not inherit from NSObject, e.g. NSProxy, also implement the NSObject protocol, so they support all of the essential features of NSObject.

