---
layout: page
title: ClassInheritance
---



ClassInheritance is a central concept to ObjectOrientedDesign. It allows an exceptional amount of code reuse by allowing classes to inherit properties and behaviours from other classes.

An example:

Say we had a class (called "Foo") that had the data members "name", and "address", and the methods "name", "address", "setName:", and "setAddress:".

Next, we define a SubClass (called "Bar"). This SubClass specifies the data member "phoneNumber", and the methods "phoneNumber", and "setPhoneNumber".

The first class, Foo, has a name and address, and methods to get and set both of those.

But the second class, Bar, has many more than what we gave it. It has a name, address, and phoneNumber, and methods to access all of them, because it inherited the name, address, et cetera, from Foo.

The class that a class inherits from is its SuperClass, while a class that inherits from a class is a SubClass.

ClassInheritance provides code reuse by allowing you to take a ready-made class, and adapt it (often referred to as "specializing its behaviour") to your specific needs.

