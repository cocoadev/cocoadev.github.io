---
layout: page
title: SharingVariablesBetweenObjects
---

I want to know something that seems basic knowledge. Is it possible that an object can modify a variable of another object ? Maybe it is not, but if it is, how can I do so ?

I have a subclass of NSView and a custom NSObject. I want that custom object to get access to one of the variables of NSView. In other words, I want my custom object to modify an array that is declared in the implementation of my NSView.

Can anyone bright some light to this interrogation ?

-- Trax

----
This is probably already outlined somewhere (I'm being too lazy to look unfortunately), but I believe you are referring to a class's accessor and mutator methods. To modify a variable in one object from another, you just need to create a method in the other class that will modify the desired variables according to the arguments of that method. Then a simple call of that method from anywhere else with the appropriate arguments will cause the other object to change its own variables in keeping with the standard OOP model. If this totally baffles you, I'd suggest you (re)read ObjC.pdf. --KevinPerry
----

