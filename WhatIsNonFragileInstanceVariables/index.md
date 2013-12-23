---
layout: page
title: WhatIsNonFragileInstanceVariables
---

Describe General/WhatIsNonFragileInstanceVariables here.

Can anybody know the non-fragile instance variables? What it does and how to use in a program?

Thanks

----
I doubt it. Can you provide some context?

----
I mean what is the fragile base class problem in objective C and what is solution for it?

----

The "fragile base class" problem is common to all object-oriented languages. It refers to the way derived classes (subclasses) can stop working when changes are made to a base class (superclass). This problem is not commonly discussed in Objective-C circles, perhaps for the following reasons:

*The vast majority of Objective-C code is based on the Foundation Kit and the General/AppKit, both of which have been around for years and tend not to be subject to radical change
*The dynamic typing of the language itself protects code from some of the nastier side-effects to which statically-typed languages can succumb 

At the very least, I can tell you that it's never been much of a problem in the code I've written (provided I think a bit before I start coding about how my own stuff talks to itself).

----

In Objective-C, the main issue is that you cannot add instance variables to a class without recompiling all subclassers.  So, Apple cannot add any ivars to anything in the General/AppKit, because they cannot recompile third party code.  If they need more space in a class, they have to pull some hack or other to get it.

----
Objective-C suffers from a much less severe form of the Fragile Base Class problem than many other languages.  <http://en.wikipedia.org/wiki/Fragile_base_class>
Here is one of the seminal references to the Fragile Base Class (Or Fragile Binary Interface <http://en.wikipedia.org/wiki/Fragile_binary_interface_problem>) problem as encountered with C++: <http://www.beatjapan.org/mirror/www.be.com/aboutbe/benewsletter/Issue79.html>

----

A simple way to get around the problem is to place some or all of your instance variables into a struct or private class, then have a pointer to it in your ivars. If your original class looked like this:

    
@interface Foo : General/NSObject {
   int x;
   id y;
}
@end


You could redo it like this:

    
@interface Foo : General/NSObject {
   struct General/FooInternal *internals;
}
@end

// in the .m file
struct General/FooInternal {
   int x;
   id y;
}


The init method would malloc the struct, dealloc would free it. This indirection allows you to add or remove things from the F<nowiki/>ooInternal struct without breaking binary compatibility anywhere. Apple has been doing this a lot in recent releases. A lot of classes that were created for 10.3 or later are created this way. See General/WebView and General/NSURLConnection for two examples.

----

Don't forget to mention how important it is to NOT publish the structure of     General/FooInternal. Anybody that subclasses     Foo should never access members of     General/FooInternal. The comment "in the .m file" is a key point that cannot be overlooked. --zootbobbalu

----
Sure it can. You put normal instance variables in the .h file and count on other people not to access them, both because the compiler enforces it (unless you use @public) and because it's against the convention. Publishing     General/FooInternal in the header is pointless but certainly not any worse than publishing other ivars.

----
I should be more clear on why it is important to hide the structure of the ivar     internals. This discussion is trying to explain what a non fragile instance variable is. The key point to be made perfectly clear is the importance of never directly accessing     General/FooInternal members. You are correct that the compiler will not allow direct access if     internals is private. Hopefully the reason why this should not be done is getting across here. Sample code to demonstrate how subclass ivar offsets are altered when an ivar is added/removed from a parent class could be useful, but the main point to be made is why using a pointer to a structure is non fragile. --zootbobbalu

----
Except that directly accessing members is not the important thing when considering fragile base classes. Yes, doing it will get you in trouble if they change, but it's so rarely done (at least in General/ObjC) that it's not really important to consider.

The important point to note is that you will get in trouble if your superclass adds instance variables, even if you are not accessing them yourself in any way. If the subclass is not recompiled, then its ivars will overlap in memory with the ivars of its superclass, and confusion will ensue. This is the key point, since almost all of our classes have superclasses. Talking about direct ivar access just confuses the issue IMO.

----

*Sure it can. You put normal instance variables in the .h file and count on other people not to access them, both because the compiler enforces it (unless you use @public) and because it's against the convention. Publishing     General/FooInternal in the header is pointless but certainly not any worse than publishing other ivars.*

Actually this is wrong. Default ivars (not designated as public or private) are directly accessable to all subclasses. So if you publish the members of the structure, users of the base class will be tempted to "->" these members.

**General/BaseClass.h**
    
@interface General/BaseClass : General/NSObject {
	struct General/FooInternal *internals;
}
@end

@interface General/SubClass : General/BaseClass {

}
@end


**General/BaseClass.m**
    
struct General/FooInternal {
	int count;
	float rate;
};


@implementation General/BaseClass
- (id)init {
	if ((self = [super init]) && 
		(internals = malloc(sizeof(struct General/FooInternal)))) 
	{
		internals->count = 1;
		internals->rate = 10.0f;
	}
	return self;
}
- (void)dealloc {
	if (internals)
		free(internals);
	[super dealloc];
}

@end

@implementation General/SubClass
- (void)test {
	General/NSLog(@"count: %i rate: %f", internals->count, internals->rate);
}
@end


*Except that directly accessing members is not the important thing when considering fragile base classes*

It IS important if a pointer to a structure is used as a solution to the fragile base class problem (e.g. adding/removing members to this structure could cause problems if a subclass is "->" members directly).  

*Yes, doing it will get you in trouble if they change, but it's so rarely done*

To a user of your code yes, IAOI they follow the rules. But what if you stubbornly insist on publishing the members of the "internal" structure just to prove a point and your code becomes the foundation of a large project. Are you really that confident that everyone will be on the same page? Why not hide that which will get others in trouble? Apple does it, why shouldn't you?

*The important point to note is that you will get in trouble if your superclass adds instance variables, even if you are not accessing them yourself in any way. If the subclass is not recompiled, then its ivars will overlap in memory with the ivars of its superclass, and confusion will ensue. This is the key point, since almost all of our classes have superclasses.*

I'll have to agree that this is the most important point, but if you get sloppy with the solution provided here, you can still fall into the same type of trap. --zootbobbalu

----
You seem to be taking this a little too strongly. I'm not saying you *should* publish it, merely that it does no harm. Accessing your parent class's ivars is generally a bad idea. You've written an awful lot about something that is trivially fixed by adding @private to your @interface. And yes, of course, this is trivially fixed by moving your struct into your .m file too. But again, this only fixes things you shouldn't really be doing in the first place.

----

*Accessing your parent class's ivars is generally a bad idea*

I agree.

*But again, this only fixes things you shouldn't really be doing in the first place.*

I also agree, hopefully anyone who reads this will "get it" too.

*You've written an awful lot about something that is trivially fixed by adding @private to your @interface. And yes, of course, this is trivially fixed by moving your struct into your .m file too*

You know that, I know that and it is probably trivial to experienced Cocoa developers. I'm just trying to help people unfamiliar with the topic. --zootbobbalu
