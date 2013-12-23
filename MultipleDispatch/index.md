---
layout: page
title: MultipleDispatch
---



MultipleDispatch (also known as DynamicMethodDispatch) is a concept that allows you to write several methods with the same selector, but which take different types of objects, e.g. a method such as doSomething: that takes an NSArray and a method doSomething: that takes an NSView. Then, you would simply make a call to doSomething, and the system decides which method to use based on what type of object you pass it (at runtime, of course).

MultipleDispatch is often used as a replacement for things such as CPlusPlus's templates and function overloading.

ObjC does not have this implicitly, but you can fake it fairly easily, by  making a single method, something like doSomething:, that takes an NSObject, and decides which method to call by querying the passed object as to its class.

In this system, an NSArray might be passed on to doSomethingWithArray: or an NSView to doSomethingWithView:

 ----

That's not really MultipleDispatch. MultipleDispatch means that method selection is not limited to one
argument (or based on the class of the object you send the message to - in Smalltalk speak), but done over all (possibly more than one or two) arguments.

SingleDispatch is the simple case of method selection and the most common case, where method selection is done over just one argument (the object you are sending the message, to - in Smalltalk speak). Most object-oriented programming languages are supporting SingleDispatch.

DoubleDispatch then is a another simple case, when method selection is done over two arguments.

In CPlusPlus it may look like it has MultipleDispatch, but it does only select the method at compile time (it has to know the methods in this case) and not at runtime. So this is not really object-oriented MultipleDispatch, which would mean dynamic method selection at runtime.

The first widespread use of MultipleDispatch was with the Common Lisp Object System. It is also provided in DyLan.

----

I agree to the first thing mentioned is not really known as MultipleDispatch but lately has been coined DuckTyping.

----
I know there is a time and place from almost anything, but can someone pronounce on the general goodness of MultipleDispatch.  The overhead is obvious: extra method invocation, a class querry and whatever overhead your branching unit can't shave off for you resulting from your flow conditionals.  Can someone list some cases where you really need this?

*Whoops, edit collision. Not sure if this is what you're looking for, but in e.g. HaskellLanguage, you use MultipleDispatch to implement e.g. special cases. Although I'm not sure if this counts as multiple dispatch because as I recall it switches not on the type of the argument but on the value, e.g. you could have a function f(x) defined as x where x != 0, and defined as 1 where x = 0.*

----
Firstly, an object's class should never be queried. It is poor style and leads to maintenance problems when you try substituting a class that is not recognized. Instead the idea should be something like TellDontAsk, where you tell an object what it should do and it should decide how to do it. MultipleDispatch is fundamental to OO and ubiquitous. A lot of people use it without realising. 

'DuckTyping' or whatever you want to call it is an alternative and is also ubiquitous, for instance compilers like gcc rely on it for greatly simplifying compilation, where parsed structures know how to compile themselves. This avoids switch statement smells and can greatly reduce code size and maintenance, etc. It is the basis of many of the OO design patterns.

Check out the definition from http://www.c2.com/cgi/wiki?MultipleDispatch :

*When a dynamic dispatching mechanism for a function uses more than one piece of information to determine which function to actually call. Usually the pieces of information are the types of the parameters passed to the function.*

This definition sums it up pretty exactly. Note it says nothing about methods or objects, just about functions, and the information used to decide which to call. Say you have a conditional. Each branch could be extracted into a function, or a method on a class. A switch represents a group of classes. The second definition of MultipleDispatch is exemplified by languages such as Haskell, Lisp and StandardML, where pattern matching can be performed on the arguments to decide which function to use. Similar functions tend to clump together, similar to a switch. 'DuckTyped' methods are functions associated objects. These functions are separated from each other.

The viewpoint about overhead is understandable but one must also realise there are other more important issues besides raw speed. Shaving a method call off here and there to 'optimize' code can lead to major maintenance headaches down the line, usually where you just end up rewriting all your code to use MultipleDispatch or just scrapping the lot. 

The extra information doesn't just have to be the arguments and target type, for instance, Perl functions can change depending on whether they are being used in scalar or list contexts. 

Sorry I didn't list any cases, but if you want to see some of the major patterns where multiple dispatch is used a good place is http://www.dofactory.com/patterns/Patterns.aspx -- MikeAmy

