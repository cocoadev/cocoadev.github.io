---
layout: page
title: AnObject
---



An object is the basic unit of data in Object Oriented Programming and Design. Unlike primitive data types, an object contains not only its data but also the methods necessary to describe and alter its data. (Code, after all, is just a special kind of data: a set of instructions given to a computer.) An object will generally have data which can be described as either an attribute or a relationship (composition (composed of data/other objects) or inheritance (derived from other objects)). 

Objects allow you to solve problems in a more flexible and lifelike way which is the reason for having Object Oriented programming languages. This model allows for modularity within an application which provides a number of advantages - the advantages of using ObjectOrientedDesign.


----
** Terminology Time: Objects and Classes **

You might wonder just what the relationship between objects and classes is. Basically, it's like this: ** A class is a blueprint, an object is a house. ** (A ClassMethod is an exception, but we'll ignore that for now.)

The code you write will form classes. At runtime, you can instantiate (i.e., create) objects that are instances of the class you wrote earlier. Or instances of classes someone else wrote, like the NSString class.

All instances of a class share the same methods (code) to act upon a DataMember or InstanceVariable, but they do not share the DataMember. For example, every NSString object has a method that returns its value. This method is defined in the NSString, the blueprint for the objects. However, every NSString object has its own value - one might be "Hello", another might be "World". Thus the methods are the same, but the data they act upon differs for each object.

Again, this is similar to the house example: Several houses may share the same blueprint, but they don't occupy the same place, they're not made out of the very same bricks and they don't contain identical furniture or people living inside of them.

In the Objective-C language, classes are also objects. Albeit class objects have only methods and no data members. In general, a ClassMethod is invoked as a FactoryMethod for creating an InstanceObject with data members initialized to specific values.

So, in Objective-C, one should think of an object in terms of ClassObject and InstanceObject.

----

Ideally, any code outside the object does not need to know what's happening inside of it - this is known as encapsulation (one of the fundamental principles of good Software Engineering).

To understand the advantages of this, you need to look no further than Apple's NSString class.  NSString is actually not a single class but an example of what is called a ClassCluster. (A ClassCluster is an important DesignPattern in Cocoa.) When you create an NSString, Cocoa actually creates an instance of a certain kind of CoreFoundation string object - which kind entirely depends on the nature of the string: It might be an object for a plain ASCII string if that's enough to store your data, it also might be a UTF-8 object if your string contains non-ASCII characters. 

The important thing is: **you do not need to know anything about this**. 

Because all of these going-ons behind the scenes are encapsulated within the NSString class, all you ever see is an instance of NSString. Should Apple decide to completely alter the way NSString behaves internally to improve its performance, you wouldn't know the difference as long as the new NSString class would still understand the same messages as the old one. And, more importantly, your code would automatically take advantage of the new NSString class without needing a change.

This also explains why it's a bad idea to use private APIs (i.e. object messages that aren't cleared for public use by the developing company - these methods typically are either preliminary and still subject to change or they're only meant to be called from within the object, not from the outside): By doing so, your code depends on implementation-specific internals of AnObject, breaking its encapsulation. As a result, any update to the owner of the private APIs may break your code if its developer decides to change the object's implementation.

----

When you use objects in your ObjC code, you are actually using pointers to those objects in memory. Because of this, you can have multiple pointers to the same object. This may help your understanding of concepts such as DynamicBinding and DistributedObjects. -- RobRix

For more, read ObjectsStructsAndPointersBasics and, if you have not already, MemoryManagement and AccessorMethods

