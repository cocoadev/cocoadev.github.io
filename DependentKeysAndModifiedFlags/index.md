---
layout: page
title: DependentKeysAndModifiedFlags
---

Hi, gang. I have an entity with several transient keys such as "syncState" which uses two other keys ("published" and "modified") to determine whether the entity requires synchronization. Regardless of 'better ways to do it' (it needs to be this way for several reasons not relevant to this discussion), I need to know how to make *any* key trigger the entity to flag "modified" = YES.

In other words, I understand completely how to trigger changes on the dependent key "syncState" and that's working just fine, but I want *any key change* to cause "modified" to be set to YES. How do I do this? The only way I can think is to write custom accessors in an NSManagedObject subclass and set this value any time a     setFoo: method is called. Surely there's an easier way.

----

**Solved**

Apparently, subclassing and overriding     - (void)didChangeValueForKey:(NSString *)key is the correct course of action. The one caveat, however, is that the dependent keys and the "modified" key shouldn't be touched during this method or it'll just loop and die. SO - I set up an if condition. If the value of 'key' is "modified" or any dependent keys, ignore it, otherwise, flag it modified. Works perfectly.

