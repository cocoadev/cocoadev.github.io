---
layout: page
title: PredicateForManagingIndirectRelationships
---




I am building an iTunes-like browser for my CoreData bibliographic database.
This is the entities scheme

http://www.cnomania.it/garage/entities-relationships.jpg

My browser follows this order : topics-author-entities-passages

http://www.cnomania.it/garage/browser.jpg

After a few unsuccessful attempts, I am trying to manage this browser through the filterPredicate binding.
Now I have the first big issue.
I cannot really form a predicate that chooses the  author that is linked to a topic. I tried something like


NSPredicate *result= [NSPredicate predicateWithFormat:@"entries.passages.topics.name = '%@'", selectedTopicName];


Yet I got no result whatsoever. The predicates gives me an empty set.
Any suggestion?
Cheers,
DavideBenini


----

Try "entries.passages.topics.name == '%@'" ... Note the double equals.

