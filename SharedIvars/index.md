---
layout: page
title: SharedIvars
---


Shared Ivars

 I have an instance variable which returns a double value in the myDocument file in a document based application. I want to use that value in another class. How do you get the value from one class to the other. eg.
in "myDocument.m"
 -(double)value1 { return value1; }

in myClass.m -(double)totalValue

{ return [self value2] + (value1 from myDocument class) }

Value2 is already in myClass, how do you get value 1 (from myDocument)  into myClass???? Thanks DSG

----

Not sure if I have understood, but I'll try:
 - you get a reference to the myDocument somehow, let's say by initializing the myClass object like this:

 
 @interface myClass {
    myDocument* doc;
 }
 
 - (id) initWithMyDocument:(myDocument*) doc;
 // methods...
 
 @end
 
 @implementation myClass
 
 - (id) initWithMyDocument:(myDocument*) aDoc {
     if (self = [super init]) {
         doc = aDoc;  // assumes this object is initialized
                           // by myDocument, so we get a weak
                           // ref which is both faster and avoids
                           // retain cycles.
     }
     return self;
 }
 
 // ...
 
 - (void) myMethod {
    // ...
    someValue = [self value2] + [doc value1];
    // ...
 }
 
 // ...
 
 @end
 

Somewhere in myDocument, when you create the myClass instance, you may do:

  myClass* inst = myClass alloc] initWithMyDocument:self]; 

A faster way would be to use outlets in the [[InterfaceBuilder file for your document (create an instance there of myClass with an outlet and attach that outlet to File's Owner -- that is, the myDocument instance).

Remember that a **class** is just a blueprint for **instances**, that is, a class has no "values" of its own; it's just a model used to create instances, which hold these values (it might help to think of the class myDocument as "the abstract concept of a myDocument", which is then used to create any number of documents on-screen -- the instances of this class. Each of them will have a different value1, and you must make sure the instance of myClass references the right instance by getting a reference to it, either manually as above or with outlets).

