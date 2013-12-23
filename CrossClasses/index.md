---
layout: page
title: CrossClasses
---

Hi!

I am fairly new to the idea of Cocoa as I have just moved from Visual Basic. 

I was wondering if someone could explain to me how to call a function across a class, for instance how to get a variable from another class.

----

First off, classes don't have variables.

To get send a message to an object, use the following notation:

[destObject methodName:argument];

You really should learn C, and then read ObjC.pdf, included in the Developer documentation.

----

I should probably go into a fair bit more detail as all I did was sound like a newbie. I know a fair bit of c so there is no problems there and I know that Classes don't have variables, instead they contain pointers.

What I want to do is have a class with an NSDictionary in it. I want to be able to call to get this NSDictionary from another class.

I have this;

(NSDictionary *)dict
{
      return dict;
}

Which handily will return dict from inside the class via [self dict];

What I want to do is call that function from another class to get at the dict. How can I achieve this?

----

Try [myObject dict].

I assume everywhere you use the word "class" you mean "instance of class" (AKA "object")? A class is not an object. Also, we use the word "method", not "function", mainly because the syntax is different.

-- KritTer

