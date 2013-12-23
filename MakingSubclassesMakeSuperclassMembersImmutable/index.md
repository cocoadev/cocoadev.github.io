---
layout: page
title: MakingSubclassesMakeSuperclassMembersImmutable
---

Is it possible for Class B to inherit from Class A, and override the mutability of Class A's instance members?

----

Technically, yes. Don't do it, though, as it violates the interface that's supposed to be guaranteed by providing those methods. Better is to make Class A inherit from Class B, and don't have those mutate methods in Class B at all.

