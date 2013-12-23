---
layout: page
title: NSEnumeratorCount
---



Hi, 

Does anyone know of a way to find the number of items in an NSEnumerator, without resorting to turning it into an NSArray, doing [NSArray count] and then back into an NSEnumerator again?

I am just trying to provide a GUI update on progress as I enumerate, as I will be enumerating through large numbers of items at times.

Thanks!

----
Get the count of the container when you start, and store it somewhere. Every time you invoke     -nextObject, decrement your count by one.

----

Thanks for the advice!

