---
layout: page
title: CallingCMethods
---

I am a relative newbie to Cocoa programming and have been wondering a few things.

I know how to call between Obj-C classes fine, but how do I call methods in C++ or C in another class?

----

*Note that, strictly speaking, C does not have the concept of methods. C uses functions to separate program flow control into groups.*

The same way you would in C++ or C. For example, calling printf is as simple as writing:

    
printf("%d bottles of your favourite beverage on the wall\n", number);


----

C functions can be called directly as mentioned above.  For C++, you will need to access the methods through an object
(static or dynamic) or the class.

Static object example:

    
SomeObjectClass  obj;

obj.foo();


Dynamic object example:

    
SomeObjectClass  obj = new SomeObjectClass;

obj->foo();


or class access (note the use of ::, the scope resolution operator):

    
SomeObjectClass::foo();


All are well documented in almost any introductory C++ programming text.

Note:  The methods must be specifed to the compiler in advance which means the header file must be included!

----

Note that if you want to call c++ from objective-c, you can, but you have to rename your file from .m to .mm.  (Good point!)
Also, there are limitations -- some c++ libraries will have functions or vars named id, which conflicts with the objective-c keyword. In those cases you'll have to write some sort of "quarantine" interface (which I've been doing in my own work, bridging the ODE physics SDK to cocoa via such quarantining). 

Further, I've had trouble with prototyping namespaced c++ classes in objective-c headers; but it could just have been my own idiocy.
ShamylZakariya

---

Generally, the term "wrap/wrapping" is used for the usage of  "quarantine/quarantining" above.

---

Normally I would say "wrap/wrapping" but in this case it's not a matter of logical abstraction, but instead a matter of completely hiding the internals of another system whose *headers* alone will confuse/anger the objective-c/objective-c++ compiler. I guess it's a matter of personal preference, but in this case the word quarantine seemed more appropriate, even if it's not official terminology ;)
ShamylZakariya

