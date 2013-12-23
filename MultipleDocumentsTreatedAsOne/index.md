---
layout: page
title: MultipleDocumentsTreatedAsOne
---



I have been digging through this site and Apple docs, but I am still lost. 
I am **very** new to cocoa, so please exuse me if I am missing something fundamental.

I am seeking design advice on approaching the following problem:

BASIC DESIGN:

I have a "users document" and a "data document"


* The data document is occasionally edited, but mostly viewed by the user.
* As the user views the elements of the "data document", statistics are gathered for each element viewed by the user.
   I.e. how long it too to read, what the user thinks of the information etc...
* These statistics are recorded in the users document (filed unde the entity for the user) 
* The users document is loaded automatically (found a good solution on this site)
* The data document is explictly loaded by the user
* The users interation with the data document is stored in the users document
 

The initial problem I am working to figure out is:
 Just how do I set this up with the Cocoa Bindings \ document architecture?

I have gone through many "tutorials" and have a feel for the basic "one to many data model- attached to a window\controller idiom". 

But I am struggling to figure out how to use those mechanisms to design a solution for the above problem.

It seems as though a few key overrides will do the trick, but as I spend (hours upon hours) reading through the Cocoa docs, my feeling of being "buried" grows...

Any help on design approach **GREATLY** appreciated. 

Brian :)

----
(brian again)

More digging on this today- What I am trying to do is to make a persistent document- but manually control how it is loaded and saved.. So far no luck. The hope is if I can figure THIS out- and (maybe?) attached both documents to the same context- I can then have one controller actually have different UI elements bound to different documents....

I am also going to run throguh the core data tutorial again and see if that provides any clues..

Anyone wiling to provide a POINTER on how I should be approaching this most appreciated.

Thanks, 
Brian

----
Hmm.. continuing to dig. Went to book store last night and bought all the cocoa books I could find. One is "Step into XCode". It is looking like I will have to manage the second documebnt COMPLETELY by hand sans cocoa bindings.. Thought I would update the page. If I find a solution I will post it.

Why is this so convoluted? I would think SOMONE would have run into this before (I.e. are there NO applications with need more than one document open at a time?).

In any case, please wish me luck.

Brian

