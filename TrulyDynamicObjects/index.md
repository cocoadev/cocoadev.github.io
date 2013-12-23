---
layout: page
title: TrulyDynamicObjects
---



Objective-C is one of the most dynamic object-oriented languages. It uses the concept of messaging, in which you can send any message to any object. You can override methods of existing classes/objects, pose classes as different classes, and do many other useful tricks due to the dynamic runtime. The one area which is still static, is the objects themselves.

In Objective-C, an object is defined as a pointer to an object structure, but if we defined it as a pointer to a pointer to an object structure?

Firstly, lets define what a truly dynamic object is. A truly dynamic object is an object which has a complete control over itself. It can spontaneously go away or mutate itself to a completely different object (which doesn�t have to be in its inheritance tree). After an object mutates itself, all other code which kept a reference to it should use the new (mutated) object. This behavior can be accomplished by using only pointers to objects, or redefining an object as a pointer to a pointer to an object structure. It�ll also require the object to be aware of itself which can be done by adding a self field to the object structure.

Using Truly Dynamic Objects enables sophisticated designs that were never possible before. The most obvious design that will greatly benefit from TDO is class clusters. Lets take a collection class cluster as an example:
A collection will be created using two specialized classes: one optimized for a small number of objects, and another optimized for a large number. When a new empty collection is created, the class which handles small number of objects will be used. While objects are added to our collection, it�ll grow until it can�t handle the number of objects it contains. It�ll then mutate itself to the second type of collection which is optimized for lots of of objects. It can also go backwards an mutate again when it shrinks.

Another use of TDO can be for a class which operates on the network. When the network connection dies, this object will mutate itself into another object that�ll record all messages sent to it. When the computer goes back online, the original object will get all messages sent to it while offline, process them, and go back to its original state.

- General/OfriWolfus

----
If you don't mind the massive messaging performance hit, you can easily do this by subclassing General/NSProxy and using message forwarding.

----
I'm sure there are important uses for this sort of thing, but the way it's described above makes it sound rather like a solution in search of a problem. Consider your last example. Why not simply create a network connection object that knows how to cache messages when the network is down and send them when the network comes back? Why is it necessary for the object to morph into something else in order to provide this alternate behavior? What, exactly, does the morphing add? After all, if the object is going to morph back into its original state, then all of its original state information has to be preserved.

Objective-C does in fact do something like this, by the way. When you initialize an object, the initializer may deallocate the original object and replace it with a different, presumably more appropriate object. That's why you usually say something like "if (self = [super init]) {..." in an initialization routine. [super init] may return something other than the original self pointer, and that return value might even be nil.

If you really do have a need for this much dynamism, I think you could easily implement it in your own classes without having to resort to using pointers to pointers to objects. Instead, create a single class that contains an object pointer and passes all messages to the object it contains. Instances of your class are then free to change their contained object at will, and clients of your class don't need to know a thing about it. This is essentially what General/NSProxy does, but you may be able to avoid some of General/NSProxy's overhead by rolling your own. -CS

----
You may indeed consider this as a solution to a problem. It's pretty much like comparing an OO language to a functional one - a functional language has its own kind of classes and instances as structures and specific functions for them, but a OO language just makes them "native". Objective-C does this, but only in initialization time, and this doesn't give you the flexibility I'm talking about. At init time the object knows very little about the environment in which it's going to be used, and therefor, may not do the *best* decision about what object to actually return. When you say a "more appropriate object" may be returned, you're right, except usually there's no way for the object to know what object will be more appropriate. That's the entire point of my idea - let the object become a more appropriate one as needed.

About my last example, lets take an object named General/MyNetworkObject that can respond to -sendString:, -sendInt:, -sendFloat:, -sendStruct:, -getString:, -getInt:, -getFloat: and -getStruct:. This will be the common pattern of the implementation of all of them (typed in safari):
    
- method:arg {
 if ([self isConnected]) {
  ...
 } else {
  [messagesArray addObject:General/[NSDictionary dictionaryWithObjectsAndKeys:General/NSStringFromSelector(_cmd), @"sel", arg, @"arg", nil]];
  return something;
 }
}

Using a messages recorder object saves all this repeating code and allows the generic recorder class to be used for other stuff as well. Morphing the object doesn't necessarily mean you through the original away. You can always keep it around in a "private place" you know about, and reuse it as needed.

Wrapping all my classes in a proxy is not good enough as it's going to be *really* slow (need to allocate double number of objects and send every message twice - assuming i use my one optimized proxy which doesn't create an General/NSInvocation for every message).

- General/OfriWolfus
----

Oh, it's far slower than you make it sound. My tests indicate that forwarded messages have approximately one hundred times the overhead of normal messages. But even given that, why do you think it will be "really slow"? Much code is written in Python, Perl, etc. which do all kinds of things slowly, and many apps still run fast. Slowness is always relative.

----
Forgive me, Ofri, but I don't see the code you give above as a very convincing example. Certainly your class could be designed such that sending data over the network is done in one method rather than four, particularly if saving "repeating code" is a concern. Furthermore, all those methods would presumably need to check the status of the network anyway so that the object could morph itself as necessary. The recording code still needs to be implemented somewhere, and I still don't see how putting it in a separate object really improves things. In this particular example it seems that all you're really doing is using the object's type as a state variable.

On the other hand, splitting your code between two different classes adds significant complexity. For starters, there's the need to add and maintain code that decides when to do the morphing and then does it. More importantly, you create a situation where multiple classes must implement the same interface. If they don't, then clients can't know what messages to send to an object because they can never be sure of its type. Debugging code where the objects can change their type at arbitrary times sounds like a nightmare.

Your idea of replacing object pointers with handles (i.e. pointers to pointers) may be interesting in theory, but it's simply not realistic. It's not how Objective-C works, and the changes to the language that you're proposing simply aren't going to happen without some REALLY compelling reason. If you want to experiment with this idea within the context of Objective-C, I think your best bet is a proxy-based approach. -CS


----
There is nothing wrong with what Ofri is suggesting, it's just that it's not necessary to go to such lengths as to redefine what an object is. The behaviour described above is called the State pattern. "Allow an object to alter its behavior when its internal state changes. The object will appear to change its class." (http://c2.com/cgi-bin/wiki?General/StatePattern). It's implementable in every OO language from C++ to General/JavaScript.

What you have is an abstract superclass (or an interface/protocol) which serves as a public interface, and two or more concrete subclasses which implement the actual logic. The public object forwards all method calls to an instance of a subclass, and when it needs to change its behaviour it switches to another subclass.

 --General/TheoHultberg/Iconara
