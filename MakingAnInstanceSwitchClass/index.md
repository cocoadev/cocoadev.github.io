---
layout: page
title: MakingAnInstanceSwitchClass
---

Is it possible to make an instance of class A switch to being an instance of class B, if class B is a subclass of class A, or if class B is a super class of class A, but class A defines no new instance members?

*Yes, if you're into hacking the runtime. Look at     struct objc_object in     /usr/include/objc/objc.h - if you cast any object to     id, you can change its     isa Class to the one you want.*

Do you think that doing this kind of class switchamaroo could introduce all sorts of unexpected issues?  In other words, is this bad design?

**Yes, this is terrible design. What you want to do is use NSObject's poseAsClass: method, which tells your object of class B to pose as an object of class A, given that B is a subclass of A. It's probably better to use a category for this functionality, though.**

*I fear that may miss what is aimed at, though. Best thing is to tell us *what* you want to achieve, and then we can brainstorm about how to do it. Even ClassPosing is a tad evil, after all!*

Right.  Class posing won't do it all all, I think, because as the name suggests, it doesn't operate on a per instance basis.  Imagine the following scenario.  You have some class that is essentially a variable.  However, variables, of course can be of different types.  Traditionally, they can be ints, bools, floats, ids, and so on.  But if we want a class, with certain members to dynamically be able to go from say an int to a string (pretty classic example), we simply want to switch in some methods, switch some methods out, and if possible even move some instance members in and out... (for example).  I started a new discussion about deallocating self and reallocating self to accomplish this... but essentially, that wouldn't really do the trick, because obviously, changing self, wouldn't change all the pointers that are pointing at the given instance.  :(

*Well, one option is to just store your objects in a dictionary and look them up with a key! Changing object is now easy. Unless you really really can't use an alternative like this...do use one!*

----

You could use a pointer to a pointer.

    
    id *objectPtr;
    id anObjectToPointTo=@"anObjectToPointTo";
    id anotherObjectToPointTo=@"anotherObjectToPointTo";
    objectPtr=&anObjectToPointTo;
    NSLog(@"objectPtr is pointing to: %@", *objectPtr);
    objectPtr=&anotherObjectToPointTo;
    NSLog(@"objectPtr is pointing to: %@", *objectPtr);
    


but this is a total hack and only useful in custom classes because most all API's only allow objects to be passed by type id, not by reference to type id. --zootbobbalu

----

You could also use a NSProxy, but be aware that there is some significant overhead in this that can only be overcome by dipping into raw assembler and some **major** hacks of the ObjC runtime.

*I thought about NSProxy, but decided against it, because I also need to be able to archive these guys, and NSProxy makes archiving a pain in the but.  I posted to cocoadev on archiving an NSProxy and no one knew how, so I had to use my own hack... what I ended up doing was using one object of type *variable* and having that object store the actual variable, allocating and reallocating as need be, handling the internals of conversion... BTW, how can I optimize NSProxies?*


* **Step 1.** Write assembler code that branches to some address after altering     r12, or whichever it is that stores the current object, to the second word pointed to by said register. This is what your proxy will run when it gets a method call. Note that this address must be hard-coded into the assembler; we will change it later.     lis and     ori are your friends here.

* **Step 2.** Compile said code with *as* and figure out which bits of the resulting file are actually the PowerPC instructions for the above (as opposed to the surrounding code *as* adds for you to make a stand-alone app).

* **Step 3.** Write some code that allocates a chunk of memory with this assembler in, splices in the correct method address over the placeholder value you coded in, and stitches this method into the ObjC runtime of a given class structure.

* **Step 4.** Now tie in this madness to an actual proxy class. This step is left as an exercise to the reader. Note each object will have to instantiate its own     isa structure.


This is, by my conservative estimates, a million billion times more evil (on appropriately calibrated scales) than just altering the     isa pointer. It is also the only way of making a time-efficient proxy, something that would otherwise come in very handy in attaching aspect code to objects. Never mind.

