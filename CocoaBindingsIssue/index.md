---
layout: page
title: CocoaBindingsIssue
---


Hey,
I'm trying to write my first home grown Cocoa program, and all is not going smoothly.
I�m having real trouble getting my UI to update bindings that link to properties within other objects. I can get the UI to
update if a value is changed in the object that my object controller is linked to directly changes i.e. keypath: somekey. 
How can i get them to update when my code changes a value within an object defined in my controller? i.e. keypath: someobject.somekey?
Hope you can decipher what I�m asking and sorry if this is a really obvious question or I'm going about this the wrong way!

----

When you use bindings, the technology that causes your view to update when the model changes is KeyValueObserving.  It happens automatically when you change your model data using your KVC (KeyValueCoding) compliant accessor methods.  If your object needs to manually notify observers of a change then it can invoke willChangeValueForKey: and didChangeValueForKey:  Normally you won't have to use those methods.

