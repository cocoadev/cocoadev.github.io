---
layout: page
title: PartialArraySave
---

I have an array of dictionaries. I want to save to disk an equivalent array with a subset of the keys on those dictionaries. So, if each NSDictionary has 25 keys, I might need to save like 15. I am thinking about creating a new array and process each single dictionary, get the keys and values that I really want to save into the array, and save this new array. Does not sound like too good of a way. Is there a better way to do it?

----

There really isn't a better way to achieve your direct goal.  You'll need to walk your array, constructing a new array containing dictionaries with only the keys you care about.

A bigger, better question might be why you have an array of dictionaries in the first place.  If you had a collection of true objects, rather than just dictionaries, you could ask those objects to construct a property list representation of themselves that you could save instead.  Or you could have your model objects implement the NSCoding protocol (which NSArray also does) and just create an archive containing your data.  Or you could even use Core Data, and let it handle both the management and the persistence of your object graph, freeing you from even worrying about your array.

