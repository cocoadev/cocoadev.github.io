---
layout: page
title: RetainingImmutableVars
---



The question: should I retain or copy an object?

As a simple rule: Retain where the identity of the object is important, copy where only the value of the object has importance. If you have an NSString representing someone's name, and you are interested in keeping a reference to their name, you should retain that NSString. If that name is then changed, your reference will reflect the current name.

On the other hand, if you want that person's name as it was exactly at the moment you queried it, and no object already claims to signify that, then you would want to make a copy. By paying attention to the meaning of the code, most questions of retaining versus copying will resolve themselves.

Note that copying immutable objects has exactly the same effect as retaining them, so there is no need to break this rule to optimize your code.

