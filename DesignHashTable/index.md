---
layout: page
title: DesignHashTable
---

**Object Design:** *Hash table*

----


* part1 - Hash table basics
* part2 - Chaining http://goo.gl/OeSCu
* part3 - Open Addressing
* part4 - Hybrid schemes


----**part1 - Hash table basics**----

Hash tables are good when the number of potential keys is far too big to make an array, but a better access performance than DesignRedBlackTree's o(log(n)) is needed, or the keys cannot realistically be ordered. 

The basis of a hash table is an array with MAX entries, and a *hash function*. This latter takes a key and *hashes* it to produce a number in the range [0,MAX). Identical keys must always hash to the same number, but so may different keys - the hashed value need not be unique.

When inserting a key, or looking for one, one must start at the element of the array indexed by the key's hashed value. If two keys in the hash table never shared the same hashed value, we could stop there. What to do if a hash value is shared (a collision) is described below.

First, an example. Suppose our keys are all possible animal names shorter than 10 characters, and our hash function simply returns the number of characters in the animal name. If we started with {cat, horse, elephant} as our keys, our array would look like: 
* [1] - 
* [2] - 
* [3] - "cat" key
* [4] - 
* [5] - "horse" key
* [6] - 
* [7] - 
* [8] - "elephant" key
* [9] - 


Were we to look for "giraffe" in the hash table, we would look at table entry [7], and find it empty. On the other hand, if we looked for "pig", we would find the key "cat" in slot [3] and have to check the two for equality before concluding there were no pigs at the table.

----**part2 - Chaining**----

The simplest way of handling a collision is to keep a linked list hanging off each entry of the table. Assuming we have a good hashing function that keep collisions to a minimum, this is acceptable because few lists will have more than even one entry.

For example, on adding "pig" to the table above, we would get: 
* [1] - 
* [2] - 
* [3] - "cat" key - "pig" key
* [4] - 
* [5] - "horse" key
* [6] - 
* [7] - 
* [8] - "elephant" key
* [9] - 


This is called a *chaining* hash table, for obvious reasons, and it is by far the most common kind of hash table actually used.

----**part3 - Open Addressing**----

An alternative to chaining is to try another entry in the array when a collision occurs. This is called an *open addressing* hash table ("open addressing" just means slots other than *hash(x)* can be used to hold  *x*). To search such a table, one must use a predefined string of entries when searching for or inserting a given key, called a *probing sequence*. The simplest probing sequence is of course just to proceed linearly through the array, called *linear probing*.

On adding "pig" to our first table under linear probing, we first try slot [3], but finding it full proceed to the empty slot [4] and insert it there: 
* [1] - 
* [2] - 
* [3] - "cat" key
* [4] - "pig" key
* [5] - "horse" key
* [6] - 
* [7] - 
* [8] - "elephant" key
* [9] - 


This scheme, while requiring no extra memory to be allocated, has disadvantages. In the above, for instance, to look for a "hen" one needs to examine entries [3], [4], [5] and [6] just to tell there are no hens, since until we reach an empty slot we cannot be sure no hens follow.

It also requires we move misplaced objects in their probing sequence when other objects are deleted; after removing the cat, we would need to move the pig back to [3]. Alternatively, we can leave a "tombstone" where the cat was, telling subsequent searches not to stop on finding the seemingly-empty slot, but this is problematic as soon there will be no empty slots left, forcing searches to scour the entire array.

Linear probing has other difficulties, mainly a depressing tendency to build up long chains of occupied slots, since the longer a chain is, the more likely a key will need to be put at the end of it. More complex probing sequences avoid this at the cost of being incredibly difficult to remove objects from.

Even the best open addressing schemes have to cope with a mathematical lower bound on search times, one which hits "unacceptable" as the number of items in the hash table approaches the size of array used.

On the whole, therefore, open addressing is rarely seen.

----**part4 - Hybrid schemes**----

An alternative to a linked list of entries a la chaining is to use a more sophisticated search structure to store collided objects. Hybrid schemes welding DesignHashTable with DesignRedBlackTree work well at bounding operation times in pathological cases (like a crummy hash function or a stupidly small array).

