---
layout: page
title: NSValueTransformerUsingSelf
---



I have a CoreData entity which stores a distance float. I want to store all the distances in metric form for my calculations but I need to allow the user to see (and enter) their distances in imperial form (stupid imperial system).

A NSValueTransformer would seem almost perfect for this task. I've read the NSValueTransformer page and found that binding to self can allow return values based on multiple model attributes - exactly what I need. But how would this work for reverseTransformedValue: ? If the transformer is being passed self it has no way to discover the new value?

----
Please give your cocoa-dev post some time before you come here. Otherwise you'll waste people's time because replies in one place won't be seen by people in another place.

