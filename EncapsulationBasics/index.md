---
layout: page
title: EncapsulationBasics
---

**Definition**

To encapsulate means to hide the inner workings of something from external users. Or, as I like to think of it, separating the interface from the implementation. I believe encapsulation is the heart of object-oriented programming and is a very important concept in software development.

**Encapsulation Side Effects**

Without any encapsulation, the result will be an extremely rigid system that will be difficult to change and expand unless it is very small. As I'm sure you know, most software needs to be flexible to requirement changes - and this can be accomplished through encapsulation.

One must be very careful, however, because encapsulating the wrong areas (or adding too much encapsulation) can have nasty side effects. It can add unnecessary complications as well as ruin the overall design. Proper encapsulation, on the other hand, can simplify the design as well as improve flexibility.

**Applying Encapsulation**

How does one know what to encapsulate? By predicting what areas might change in the future and how they interact with other areas of the system. This usually requires a lot of knowledge about the system as a whole, but spending the extra time in research is well worth the results. It is also useful to encapsulate access to external libraries/frameworks that may change.

Object-oriented programming greatly aids in encapsulation because each object in itself acts as an encapsulated unit (the implementation is hidden from other objects). Polymorphism also greatly increases the possibility of encapsulation. In many cases, a single object cannot be **fully** encapsulated and therefore should be grouped with other objects to form a larger encapsulated unit. Usually, one object in the group is responsible for all communications going in and out of the group to simplify the encapsulation (Facade or Adapter pattern).

**Example Case**

If a Cocoa application needs to communicate over the network using sockets, then there are several SocketClasses to choose from (NetSocket, SmallSockets, CFSocket, etc.). Later on, one may wish to use raw BSD sockets to give the most control over performance. He decides early on to encapsulate the socket class so all interaction goes through a wrapper layer. Once this interface is established, he can use any kind of socket class as the implementation without altering any other code.

Feel free to comment. --RyanBates

----

ObjectOrientedProgramming does AFAIK consist of three things:

*DataAbstraction � use datatypes instead of data, e.g. Person instead of Name+Age+...
*Encapsulation � place logic together with the data (i.e. methods).
*Inheritance � this opens up for polymorphism.


I do not think RyanBates consider DataAbstraction and Encapsulation different, but I would, since a) the former is not really tied to OO and b) no-one would argue against DataAbstraction (AFAIK), but there are many reasons why placing the logic together with the code (as in Encapsulation) is not always desired.

Inheritance is also debated heavily, as witnessed by the recent ExtendsIsEvil thread.

What I really think RyanBates is saying is, that we should write ModularCode.

----

A very important aspect of encapsulation is that, since it hides the implementation details, you are free to change the implementation whenever you wish, as long as you stay true to the interface. 

For example, if you had a class representing a person, you could first implement it with two instance variables called "firstname" and "surname". Then, later you decide that you want to make the design even more abstract and add a class representing a name. Now you can replace the two instance variables with one name object, but your interface stays the same (just delegate the retrieval of the names to the name object), since you never said anything about your particular implementation in the interface.

That was a trivial example, but it is very common in real life. Encapsulation is also a good reason to think twice before writing getter and setter methods as described in GettersAndSettersAreEvil.

Encapsulation, as well as Inheritance and DataAbstraction, are your friends, treat them well and understand them, and you will be rewarded.

-- TheoHultberg/Iconara

----
The classic paper on this, "On the Criteria To Be Used in Decomposing Systems into Modules", by David Parnas, is at http://www.acm.org/classics/may96/.

