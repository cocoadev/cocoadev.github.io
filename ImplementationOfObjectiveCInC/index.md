---
layout: page
title: ImplementationOfObjectiveCInC
---

This page provides a summary of how Objective-C is implemented in C using Apple's Objective-C runtime.  Other runtimes are similar.

As a C programmer, perhaps you need the "magic" removed so you can see what is happening in Objective-C.


Objective-C adds the concepts of *objects* and *message sending* to the C language.  Any message may be sent to any object.  By default, there is a runtime error if the receiver of a message does not understand the message, although some receivers may suppress the error or handle the unrecognized message my passing it on to an object that does understand it.


But first, let's propose these implementation neutral definitions: **An Objective-C object is anything that can receive a message.  A message is just a request for the receiver to do something using arguments that may be provided with the message.  In some cases, sending a message may return a value to the sender.**


Those definitions may seem vague, but it is that simple, and there are huge implications to this.  Just to whet your appetite, neither the receiver nor the message needs to be known at compile time, and this is the great strength (flexibility) of Objective-C and similar languages like Smalltalk, Ruby, and Python but not like C++ or Java.


So, for a C programmer, what is an Objective-C object ?  In implementation, and Objective-C object is any C structure whose first element is a variable of type <code>Class</code>.  In <code>objc.h</code> on your hard drive, you will find the C type <code>Class</code> is declared as

    
typedef struct objc_class *Class;


Another fun C typedef is the <code>id</code> type used to store a pointer to any Objective-C object:

    
typedef struct objc_object {
	Class isa;
} *id;


So, any variable of type  <code>id</code> is just a pointer to any structure that contains as its first element a pointer to a variable of type <code>Class</code>.

Now that you know exactly what an Objective-C object is from a C programmer's point of view, what is a message ?

Apple's Objective-C implementation provides a couple of C functions:

    
id objc_msgSend(id self, SEL op, ...);
id objc_msgSendSuper(struct objc_super *super, SEL op, ...);


The <code>SEL</code> type is the C type that identifies what message is being sent.  In <code>objc_msgSend(id self, SEL op, ...)</code>, <code>self</code> is the receiver of the message, <code>SEL</code> identifies the message being sent, and any number of arguments may follow.

<code>objc_msgSendSuper()</code> is similar to <code>objc_msgSend()</code>.  The only relevant difference between the two is where the function looks first to find an <code>IMP</code> to call in response to the message.  What's an <code>IMP</code> you ask ?

    
typedef id (*IMP)(id, SEL, ...);


<code>IMP</code> is a C typedef for a pointer to a function that takes the same arguments as <code>objc_msgSend()</code> and returns the same type.  The code that is executed in response to receipt of Objective-C messages is compiled into C functions by the Objective-C compiler.  Pointers to those functions are then associated with messages using a hash table that maps <code>SEL</code> to <code>IMP</code>.

<code>objc_msgSend()</code> and <code>objc_msgSendSuper()</code> simply use the provided <code>SEL</code> argument to look-up the corresponding <code>IMP</code> and than call the <code>IMP</code> passing the same arguments that were passed to <code>objc_msgSend()</code> and <code>objc_msgSendSuper()</code>.

That is Objective-C.


Now that you know what an object is, let's look at some Objective-C code to see the corresponding C code.

    
NSArray *fileTypes = [NSArray arrayWithObject:@"td"];


<code>NSArray *</code> and <code>NSOpenPanel *</code> are both synonyms for the C type <code>id</code>.

The following code will produce **exactly** the same machine code when compiled with Objective-C as the code above:

    
id fileTypes = [NSArray arrayWithObject:@"td"];


So you know that <code>fileTypes</code> is a variable that points to a <code>struct objc_object</code>.


<code>id fileTypes = [NSArray arrayWithObject:@"td"];</code> is a synonym for

    
struct objc_object *fileTypes = objc_msgSend(objc_lookUpClass("NSArray"), sel_getUid("arrayWithObject:"), @"td");


For reference: In <code>objc.h</code> see the declaration <code>SEL sel_getUid(const char *str);</code>.
In  <code>objc-runtime.h</code> see the declaration   <code>id objc_lookUpClass(const char *name);</code>.


ignoring <code>@"td"</code> for a moment...

you now see that <code>id fileTypes = [NSArray arrayWithObject:@"td"];</code> is just a syntactically nicer way of writing 

    
struct objc_object *fileTypes = objc_msgSend(objc_lookUpClass("NSArray"), sel_getUid("arrayWithObject:"), @"td");


In reality, the Objective-C compiler can optimize things a bit to eliminate some of the function calls at run-time, but that is beside the point right now.

What is the significance of the string <code>"NSArray"</code> ?  There is an Objective-C class named <code>NSArray</code> provided by the Cocoa framework.  As you can guess from the C language implementation of Objective-C, <code>objc_lookUpClass()</code> just returns an <code>id</code> type value that was associated with the string<code>"NSArray"</code> by the Objective-C compiler.  Yes, that's right, just like the compiler generates a hash table to associate <code>IMP</code> with <code>SEL</code>, it generates a hash table to associate <code>id</code>s with Objective-C class names like <code>NSArray</code>.  So, when the compiler compiled the class <code>NSArray</code>, the compiler generated the following  things:


*the compiler generated a static C structure whose first element is of type  <code>Class</code>.
*the compiler generated a hash table entry associating a pointer to the static structure with the string  <code>"NSArray"</code>.
*the compiler generated a hash table entry associating a  <code>SEL</code> with the string  <code>"arrayWithObject:"</code>.
*The compiler generated a C function containing code to be executed in response to the  <code>arrayWithObject:</code> message selector (<code>SEL</code>).
*the compiler generated a hash table entry associating a pointer to the C function (<code>IMP</code>) with the  <code>SEL</code> previously associated with the string  <code>"arrayWithObject:"</code>.


All that stuff generated by the compiler makes <code>id fileTypes = objc_msgSend(objc_lookUpClass("NSArray"), sel_getUid("arrayWithObject:"), @"td")</code> work at run-time.

