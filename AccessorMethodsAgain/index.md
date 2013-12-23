---
layout: page
title: AccessorMethodsAgain
---

I can't find any info on the web or in my O'Reilly books about why you need accessor methods at all.  why can you not just access the variable values directly?  what is an accessor method good for?

in other words:
If I declare a variable in xxx.h
and give it a value in xxx.m

can I not access its value from any other class in my program?

what am I missing here?

----

You can access the values directly, although it's considered bad practice.
If you are using OOP, you will have objects that have different state.  It's best to
use acessor methods to access the value.  In some cases, the value may need to
be computed/updated before being accessed.

----

You might be thinking that instance variables declared in a class's interface (the declarations made in @interface { }) are external variables. Instance variables ARE available to all method implementations for the class, but instance variables are not global. Instance variables become part of a data structure that gets allocated dynamically and the only object that has permission to access this data is the owner of the data. Like the posting above says, you can hack around this restriction by sharing the memory addresses of instance variables, but this is not an object oriented way of doing things.

You might want to read about encapsulation concepts in the material below.

http://developer.apple.com/techpubs/macosx/Cocoa/ObjectiveC/ObjC.pdf

