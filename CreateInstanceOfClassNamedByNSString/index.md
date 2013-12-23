---
layout: page
title: CreateInstanceOfClassNamedByNSString
---

My background has mainly been in PHP, which allows for very dynamic code like $object->$methodName(); where $methodName is the name of a method stored as a string.  You can also do $object = new $className(); where $className is the name of a class.

I am currently working on a project where I am reading data from an XML file and would be creating instances of various classes based on this data.  I have looked into the Objective-C runtime to see how I might be able to do this, but I am wondering if there is a better way to do this that might not use the runtime library?

Imagine I have an XML file with the following defined:  <code><class>SLClassController</class></code>

I would like to then create an instance of class <code>SLClassController</code>.  But some other XML in the file might then want to create an instance of class <code>SLOtherClassController</code>.

I have already done a fairly complicated if/elseif/elseif/etc. statement to verify the name of the class and that it is valid.  Then I decided to simply create an array of valid classes and loop through that to make sure the class name is valid.  So I have a static method like this:  <code>+ (BOOL)isValidControllerType:(NSString*) type</code>, where type is the class name.

What I have discovered is that I can create an instance of the class with:

<code>SLBaseControllerClass *controller = class_createInstance(NSClassFromString(type), 0);</code>

<code>SLBaseControllerClass</code> is a super class that all these controller classes would inherit from.

So I have a couple questions:

1. Is there a better way to do some/all of this?
2. Is using runtime type stuff like this frowned upon?

Thanks for any insight/advice.
-Andrew

----
You might want to post this sort of thing over in the forums. There's nothing wrong with posting it here, but this kind of conversational question and answer tends to work better over there. But on to your questions.

This is very easy to do. Look at the NSClassFromString function. You give it a string, it gives you a class. You can then send the result the standard alloc/init pair to instantiate, or any other class methods.

As for a better way to do this, it really depends on what you're doing in the first place. If this is some kind of serialization format, check out NSCoder. If this is just to create some objects when the program launches, you may be able to embed the objects into your nibs.

Runtime type stuff is absolutely not frowned upon. It's what makes ObjC great, and we know it, so don't be afraid to use it. If you get too crazy it can make the code difficult to understand or maintain, but this is pretty mild.

