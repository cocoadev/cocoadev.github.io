---
layout: page
title: DynamicTyping
---



DynamicTyping is a relatively simple idea: you postpone specifying what type AnObject is until the program is running.

DynamicTyping is done by declaring an object as id:

    
id someObject;


id is the generic type for objects in ObjectiveCee.  The statement above means that someObject is an object, but doesn't specify what *type* of object it is.  someObject could be a window, a button, an array, or something else entirely.  It is vaguely analogous to a void pointer in C.

Using DynamicTyping allows for some interesting concepts such as MultipleDispatch, simply by making everything that much more dynamic.

