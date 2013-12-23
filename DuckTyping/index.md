---
layout: page
title: DuckTyping
---

If it walks, talks, and quacks like a duck, then it must be a duck.

For example:
    
if([someObj respondsToSelector:@selector(addObject:)])
   [someObj addObject:otherObject];

In the first line we ask if we can send it an     addObject: message, and if we can, conclude that we are dealing with an array, and adds the object.

Why is this any different than:
    
if([someObj isKindOfClass:[NSMutableArray class]])
   [someObj addObject:otherObject];


It is because objects not inheriting from NSMutableArray might behave exactly like a mutable array, and so should be allowed to be substituted for one.

This is used e.g. with bindings where a table view gets a (proxy) object representing a (mutable) array for the     arrayController.arrangedObjects binding, and treats this as a (mutable) array. The array controller itself may get its     contentArray from     model.someArray which is not an instance variable, but a set/get method (but     mutableArrayValueForKey: will create the appropriate proxy).
----
Careful, Just because an object "responds" to addObject:, you **can not** blindly conclude that you are dealing with an array, nor assume it behaves like one.

*Better would be to use     conformsToProtocol:*

**At first I thought it was just a pedantic note, but it seems you did not get the point about DuckTyping.** See http://www.ruby-talk.org/cgi-bin/scat.rb/ruby/ruby-talk/78502
----
Array here is really an abstract concept and does not refer to NSMutableArray. The library writer e.g. says: I will add the results one at a time as objects using     addObject: assuming the user will hand in an NSMutableArray, but he can actually hand in anything which responds to that method.  The object he passes would not need to store the objects in an array, it could write them to standard out or throw them away, the implementor of the library should not care...

----

DuckTyping reminds me of languages with prototype based inheritance, like JavaScript and IoLanguage. There are no classes, only prototype objects. Methods can be added at any time, and you inherit by cloning the state of another object. It's another way of thinking.

In the good ol' days of JavaScript hacking, the wise birds always used to say "object detection, not version detection". What they did was encouraging DuckTyping, since that was safer than checking for a browser version and then assuming things (i.e check if there is a document.getElementsById method before using it instead of checking for Mozilla).

Being so dynamic, how hard could it be to implement prototype based inheritance in ObjC?

-- TheoHultberg/Iconara

----

Basically, ObjC already supports it. You can enumerate all aspects of the class internals, pull them apart, modify them, add/remove methods, etc. The runtime headers (/usr/include/objc) have some good stuff in them. I suspect you could put a class together on the fly, nearly from scratch sans method implementations.

----

Yes, this is sort of already happening with bindings. I.e. a new class is created with new implementations for     set<Key>: and     <key>.  And I guess also with AspectCocoa. I had some ideas myself a while ago about adding new methods "by convention" (i.e. based on names of existing methods, the class name, instance variables a.s.o.) in the class's     initialize method (think DomainSpecificLanguage), unfortunately it turned out to be very difficult, as I did not find a way to attach user data to the methods registered, so these could only do the dispatch based on their name (or e.g. reflection, but that's sort of slow).

It would have been better if methods were registered using some sort of data structure (like Carbon's universal function pointer) instead of a C function pointer (since these can only (easily) be created at compile time).
----
Well technically, Carbon's universal function pointer is just that, a C function pointer; it's just wrapped up in a opaque type. Either way, you still have to supply the entry point.
----
Okay, I didn't do Carbon, so I do not know the details, just read it was designed so that it allowed various calling conventions (i.e. binding to different languages).

To clarify, what I wanted was just something like this:
    
struct function_ptr
{
   id(*function)(void* user_data, ...);
   void* user_data;
};

So when registering a function (method) I would hand in a structure like above, i.e. both a C-function pointer and some opaque user data which is given to the function.

This could then e.g. be a CPlusPlus object with a virtual function or an integer used in a switch/case -- but the actual C function pointer would be the same (but do dispatch based on user data).
----
*I think you may be thinking of transition vectors, which were used in PEF/CFM to implement function pointers. These could have assosiated data. However, Mach-O uses "raw" function pointers without this capacity.*

