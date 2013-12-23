---
layout: page
title: CoreDataAdvancedPredicates
---



For the purposes of this issue, I have two entities, Foo and Bar. Foo has a to-many relationship to Bar, called *bars*.

My ultimate goal is to fetch those Foo objects that have certain properties, and which do *not* contain relationships to Bar objects that also have certain properties. The natural fetch request might be:

<code>
[NSPredicate predicateWithFormat:@"(name like %@) AND NONE ((bars.x == %d) AND (bars.name like %@))",fooName,barsX,barsName];
</code>

The Predicate Programming Guide (assuming I've read it correctly) appears to back up the use of NONE in core data predicates. However, testing seems to indicate that while ANY and ALL appear to work, NONE does not- nor does NOT in combination with ANY or ALL. The above predicate, and simpler tests with NOT ANY, NOT ALL, and NONE all fail with NSPredicate complaining that it cannot parse the format string.

I've managed to work around this issue for now by fetching many more objects than I actually need and doing the tests on the resulting array. However, I imagine that if I could get the desired predicate to work, I'd get much better performance out of leveraging Core Data to do the filtering.

