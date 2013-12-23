---
layout: page
title: QtAndCocoa
---



Hi, folks, sorry my english.. I anybody know Qt? I'l try to discribe th problem...

Well, i have an app on Qt and it is only for the gui. And i have an app on Cocoa, that capturing video from web camera. As you know (?) actions (buttons) in Qt connect as SLOTs (c++ funtions). And one thing, I can't change Qt class. Also i have as you can see Cocoa class, that capturing something... So the sample

I have  xCodeObjClass class for example in Cocoa, and a function in it func1()
And i have an c++ class in Qt for example qtClass and a SLOT (function) func2() in it..
and the code is

    

void func2()
{
   [self func1]; // comments
}



comments is - i suppose i need to write better [xCodeObjClass func1]; but i doen't work to. error - _NSRaiseError
but 

    
void func2()
{
   xCodeObjClass xcoc;
   xcoc._temp = 1; // works fine, but not call of a function
}



In other words, when i'm pressing a button in Qt GUI - it runs func2() and then it MUST run func1() from Cocoa
In other in other words i must run cocoa function when i'm pressing a button

So the question is, how do i run Cocoa function from C++ function (classes)?.. c++ == qt :)

thanks..

----
The symbol *self* is not usually defined in ordinary C/C++ functions just like the C++ symbol *this* is not defined in regular C functions.  *self*  is roughly speaking the Objective-C equivalent of C++ *this* .  *self* is only available in Objective-C methods.  For background, every Objective-C method has two hidden parameters, *self*  and *_cmd* .  *self*  is the object that received the message that caused the method to be executed.  *_cmd* identifies the message that caused the method to be executed.  See http://developer.apple.com/documentation/Cocoa/Conceptual/ObjectiveC/index.html and http://developer.apple.com/referencelibrary/GettingStarted/GS_Cocoa/index.html

You are attempting to use *self*  in a regular C/C++ function.  Instead, store a pointer to an instance of the relevant Objective-C object somewhere.  In your C/C++ function, use the pointer to identify the object that should receive the Objective-C messages you send:

    
// Somewhere in your code you must create an instance of the Objective-C object
// Normally, Objective-C class names start with a capital letter like XCodeObjCClass
// The specific initializer used for the Objective-C object will obviuously depend on the class of the object
xCodeObjClass       *myObjectPtr = xCodeObjClass alloc] init];  // Objective-C objects must be allocated and initialized

// In func2(), send messages to the object pointed to by myObjectPtr 
void func2()
{
   [myObjectPtr someMessageNameTheWillBeUnderstood];
}


This is not conceptually any different from calling virtual member functions in C++.  There are subtleties having to do with the fact that Objective-C sends messages instead of calling member functions, but that is not relevant to your problem.  Just like with C++, you must identify the instance (object) that you are interacting with.  You say you are using the C++ Qt framework, so I assume you understand basic operation of C++ and the use of the *this* symbol.

Also remeber that you must release any Objective-C object that you allocate unless the object is required to exist until the program exits.  You most likely need to write 

    
[myObjectPtr release];


somewhere in your code.
----
This is exteremly introductory conceptual information that is best learned via the links to online documentation provided above.
----
If there is a desire not to create an instance of an Objective-C class, class methods can be used.
----
For posterity:
Objective-C method declarations that start with + are "class" methods aor sometimes called "factory" methods.  These identify messages that the class itself can respond to as opposed to messages that instance of the class understand.  When a method is declared with +, the corresponding message can be sent directly to the class.  The most direct example is

    
[[[NSObject alloc];


By convention in Cocoa, +alloc is a class method that returns a newly allocated instance of the class that receives the message.  In the following code, the "alloc" message is sent to the NSObject class.  Then the "init" message is sent to the NSObject instance that was returned.
    
[[NSObject alloc] init];


Objective-C class methods are similar to C++ "static" member functions.  However, in Objective-C, class methods have a self variable that represents the class itself.  C++ static member functions do not have a corresponding this variable.  Objective-C class methods are fully polymorphic, and C++ static member functions are not.

----

Ok, thanks a lot, it's solved. I'v set [myCocoaClass alloc] init] func1]; and i works.. i think so..

----
Don't forget your [[NSAutoreleasePools.

----
O, that right, thanks...

