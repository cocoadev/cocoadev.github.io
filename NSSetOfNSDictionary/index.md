---
layout: page
title: NSSetOfNSDictionary
---



I was wondering what it is the right way of creating a NSSet of NSDictionary. I want to represent a graph with labeled connection using a dictionary referencing other dictionary (a dictionary can be tought as a state). To build that representation efficiently I would like to use a set of states. Then during the building phase if the state I want to add is already in the set use the reference to the original one. 
The idea is to use a NSDictionary to represent a state and a temporary NSMutableDictionary (reused during the building phase) to represent the state I am actually working on. Then I would like to use NSSet member: method to check if there is a state with the same labeled connections and in that case use that; if it is not present create an immutable copy of the working dictionary and put that in the set.
Any ideas?

----

If that makes sense to you, go for it (didn't really make much sense to me). You can put dictionaries into sets.

----

I am try to make myself clear.
Immagine I have a graph like this:
    
A--a-->B--b-->C
       \--c-->D

I represent that with a set of 4 dictionaries: one for each state (the uppercase letters), in the dictionaries I put the connections between states.

*A = { a->(A<nowiki/>toB) }
*B = { b->(B<nowiki/>toC), c->(B<nowiki/>toD) }
*C = { }
*D = { } 

Now going on in the graph building I come along to a new state that can share the subtree starting from B. i.e.
    
A--a-->B--b-->C
        \--c-->D
E--d-->F--b-->G
        \--c-->H


*E = { a->(E<nowiki/>toF) }
*F = { b->(F<nowiki/>toG), c->(F<nowiki/>toG) }
*G = { }
*H = { } 

At this point I would like to check the set and if I could have it tested with isEqualToDictionary: instead of isEqual: I coud discover that C == D == G == H and use only C and that at that point B == F and use only B to obtain something like:
    
A--a-->B--b-->C
E--d--/ \--c--/

What I was thinking is that NSSet member: could return the set object instead of the one passed as argument and that it test for equality using isEqualToDictionary: or something like that.

----
I don't know, but this seems like it could be a case for objects or CoreData entities: A State that has a set of child States (as well as any other information, like a label). After all, a directed graph is basically a tree without an explicit root. --JediKnil

----
Trees are acyclic by definition. Directed graphs can still have cycles.

----
I think that can be done with a CFSet with an appropriate CFSetEqualCallBack. Anyone knows if the equal comparison of NSSet can be customized?

----

Read the documentation. NSSet compares objects by sending them an -isEqual: message.

