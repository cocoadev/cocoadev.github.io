---
layout: page
title: HeaderFilesReferencingEachOther
---


I know that there are better ways to do what I was attempting to do, but I still would like to know:
It seems to me that one cannot define two objective C classes that either have fields of the other class or have methods that accept parameters of the other class.
In short, two header files cannot #import each other. Is this true? Thanks.
-Henry

----

If you have a class A that needs references to class B, but you don't want to #import B's header into A's header, use a forward declaration:

    @class B;

Then just #import B's header into A's .m file. --GrahamCox

----

Two header files cannot both #import each other meaningfully. #import basically means, "I need this header file to be above the content in this file", but you obviously can't have both B before A and A before B. #import will save you from multiple includes, but it does not and can not save you from circular includes.

----

This has been a defining characteristic of C since it was created.  The way around it is to use forward declarations.  For example,

    
// Bar.h

@class Foo;

@interface Bar
{
        Foo *_foo;
}

@end


    
// Foo.h

#import "Bar.h"

@interface Foo
{
        Bar *_bar;
}


But please note that if you ever find yourself doing this you're probably doing something wrong.

----
I must disagree strongly with the "doing something wrong" comment. Two-way references in class hierarchies are quite common, especially when you consider that you need forward declarations even for parameter and return values. Consider NSView and NSWindow, for example, which both refer to each other and which both have methods taking or returning the other.

A minor nit in the example code, I would strongly recommend using @class in both, and only #import when you really need to, like when there's a struct or enum definition you use in the header, or when it's your superclass.

----

It's more of a smell than a definitive sign, I should have said.  There are of course reasons that you would do this, but often times they're not fully-conceived.  You should only use forward declarations when you know they're absolutely necessary for the end result that you're trying to achieve (not just how you plan to achieve it!).

In terms of the C++ FAQ, "Forward declarations are evil", just like "arrays are evil".  Know when you need to use them.

----
I tend to hold to the opposite philosophy when it comes to headers: #import is a smell and forward declarations should be used instead whenever possible. This not only helps avoid inadvertent circular dependencies but also makes things compile faster.

