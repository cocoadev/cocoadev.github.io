---
layout: page
title: ExtendedObjectiveC
---

After some more thought and discussions with MikeAmy I have decided that templates on classes are not as necessary. I think people should be encouraged to use protocols more often having a similar effect as what I wanted with templates.

*Formal or informal protocols? we already had a discussion on this (sorta): WhyDoesObjCNeedInterfacesWhenItHasProtocols -- formal protocols just add constraints on the flexibility and more typing by the programmer.*

I also think that it should be possible to define relations between the types of polymorphic arguments and return types. E.G. It should be possible to say that arguments type one and the return type be the same. This is it kind of what templates do but no the only thing they can do. Very similar to they way it is done in Haskell (and I assume other strongly typed functional languages).

*Are you perhaps thinking of TypeInference? yes, that would be awesome, but really, I think TypeInference, StaticTyping, Templates or whatever you want in ObjectiveCee is sort of excluded due to the use of DynamicTyping, which have other advantages (like the responder chain to name only one of the many uses in the Cocoa frameworks).*

----

I'm sure that most if not all the people here love Objective-C as I do but there are some things which I really miss from other languages. One of these things is not having generics. 

I would like to propose there addition to the language. 

*ObjectiveCee is using DynamicTyping, why would you need generics?*
- It would give you the option of using the language in a stronger typed way. You wouldn't have to use it if you didn't want to but you could. Also as you have prototypes you could request that a type conform to a prototype. You could have an OrderedList which required that the type of object it stored conformed to the prototype orderable. I see this as an advantage. 

*Not sure if you mean "formal protocols" when you say prototypes? anyway, I do not see where this would give you any "stronger typing". Types are checked *at runtime*, objects can decide *at runtime* which methods they respond to.  Could you please show some examples where generics would improve existing (i.e. typical) ObjectiveCee/Cocoa code?* I for one would like to be able to ensure that all of my NSArray content objects are of the same type in certain applications... in fact most places that return id would be nice to ensure that they are of a certain type that you can optionally specify... not to mention expressing this in code makes for cleaner documentation that doesn't get out of sync with the code (because it is the code)... 

*We already can specify types in the code, and the compiler will check them and issue a warning (but not an error).  This is because you can't know for sure at compile-time if you made a type error when using DynamicTyping.  You are really asking for StaticTyping, and this will remove the advantages of DynamicTyping, you can't have both.  E.g. if you declare your function to take an NSMutableArray as argument and enforce StaticTyping, there is no way I can call that function with the result of     mutableArrayValueForKey:, cause the result of this is not (necessarily) an NSMutableArray, similar hold for DistributedObjects, proxies etc.*

*You also seem to forget that the environment that an ObjectiveCee application is running in, is very dynamic and receives objects from other binaries, how should that be handled? E.g. what if NSString's     lastPathComponent do not return a string (but e.g. some proxy that keeps a pointer to the original string and an offset), how should the run-time enforce that this then cannot be added to your homogenous array of NSString's?*

*Worse is probably when you want to retrieve such an array from e.g. a dictionary, cause it's impractical to provide static types for an NSDictionary (unless it only maps from one type to another, e.g. NSString -> NSNumber for each element). So would you have the compiler to generate code which does a run-time check to see if all elements comply to whatever formal protocol you want the array elements to comply to?*

**As do I... I suggest instead of altering the language following a DesginPattern in AppKit... ie NSMatrix has a prototype member that all it's cells must conform to. The same could be applied to NSArray etc. Using a category to add -prototype, -setPrototype:  and then something like InvarianceCheckingWithAspectCocoa to add invariants that ensure all the contained objects match the prototype...** or instead of InvarianceCheckingWithAspectCocoa which would check on EVERY SINGLE METHOD... just add advice to before any "add" method to check the class. **The point is why adjust the language definition when the tools are already there... (besides... really who wants to muck with gcc's internals... not me)**

----

Another thing is, operator overloading. I would love it if it was possible to define operators for classes. There may be some implementation issues with this as because all your objects are pointers to objects and you wouldn't really want to overload the + of a pointer as point arithmetic would go all strange. Not that you would ever do pointer arithmetic.

*CocoaSTL adds a smart pointer for ObjectiveCee objects, with this you can use various operators like less/greater than and equal, which will send the proper methods to the objects in question. You could add other operators like this, but since it is dynamically typed, the operator probably need to send messages which all classes respond to.*

- Isn't this smart pointer used as a reference or as a instance, not as a pointer. From my experience of stl, you pass a functor for any comparison. It doesn't rely on the class implementing any operators other then possibly assignments.

*Yes the smart pointer is an instance and yes STL optionally takes a functor instead of using the normal operator, but I do not see what this has to do with anything.*

----

Basic C++ syntax (hence Objective-C++) comes to mind here. Kind of ugly, but considering you are not allowed (and should not be allowed) to redefine pointer arithmetic, I don't see a more workable solution. Other operators could be defined in a similar manner. Disclaimer, I will note that I don't actually do this in my code; In Objective-C/C++, I could see this quickly turning into a memory management nightmare.

    
inline NSMutableString* operator += 
   (NSMutableString& lhs, NSString *const rhs)
{
	[&lhs appendString:rhs];
	return &lhs;
}
.
.
.
NSMutableString *aString =
   [NSMutableString stringWithString:@"Hello "];
*aString += @"World";
NSLog(@"%@", aString);


----

I wouldn't want to add references into the language as the way pointer is to elegant. I think there must be some way around this but at the moment I cannot think how. 

*You can use a wrapper for the pointer, which is then an instance/reference.*

- But we don't have references so to are objects as I said I wouldn't add them. How do you send a message to an instance variable in Obj-C. You cannot as far as Im aware.

*I don't understand this!?! Do you know what a smart pointer is? Example (CeePlusPlus):*
    
struct my_id
{
   my_id (NSObject* obj)
   { object = [obj retain]; }

   ~my_id ()
   { [object release]; }

   operator NSObject* () const
   { return object; }

   bool operator== (my_id const& rhs) const
**\\is this not a reference?
// OK it's not a Objective-C object reference but it's a reference 
// and do you really want to have to rap all your objects up in
// smart pointers when the compiler could do it for you?**
// *The compiler already do implicit type conversion, so e.g.*
//    my_id obj = @"foo";
//    if(obj == @"bar") <- compiler will wrap right-hand-side in my_id
// *So yes, you probably do want it like this, because you anyway need a*
// *way to tell the difference between when you want the smart operators*
// *and the normal operators.*
   { return [object isEqual:rhs.object]; }

   ...

   NSObject* object;
};

*With the above we can write code like:*
    
my_id obj1 = @"Hello", obj2 = @"World", obj3 = @"Hello";
if(obj1 == obj2)
   printf("Wrong!\n");
else if(obj1 == obj3)
   printf("Correct\n");

*You could add more operators, better retain-management (overloading the copy and assignment constructor etc.  But this is the principle. So instead of id you just use my_id, and the compiler will implicitly convert from NSObject* to my_id or the other way around, e.g. you can write:*
    
my_id obj = @"Hello";
puts([obj UTF8String]);

*Unfortunately a bug in gcc makes the above spew out a warning, but the code generated is correct.  Also, unfortunately ObjectiveCeePlusPlus do not support non POD-types as ObjectiveCee instance variables.*
----

Has anyone done this before?

Where should I start looking if I where to try and extend gcc to handle these extensions?

*Probably start by downloading the gcc source-code and see if you're still hooked on doing it. An alternative might be to write a pre-processor.*

Stefan

