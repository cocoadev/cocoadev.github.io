---
layout: page
title: DefensiveCopies
---





[Discussion Start Date: Nov. 9, 2004]

When reading "how to" docs for Java, you come across references to the concept of DefensiveCopies.  http://goo.gl/OeSCu
This term doesn't seem to come up as often in Obj-C material. (Doing a search in C<nowiki/>ocoaDev today for 
the word "defensive" turned up no "defensive copy" or "defensive copies" and there is no index entries 
for the term in any of the 4 Cocoa/Obj-C books I have.)

The concept behind DefensiveCopies, though, is relevant to Obj-C so it's a bit surprising not to find it. 
Understanding what DefensiveCopies are for, and when they're needed (or not needed) can be really helpful. 
So ...

Given:
    
// Object_1.m
...

NSMutableString *myString;
Object_2        *o2;

...

myString = [NSMutableString stringWithString:@"Object_1's Mutable String"];

Object_2 o2 = Object2 alloc] init];

[o2 setString:myString];

[[NSLog(@"before appendByString: [o2 myString] = %@",[o2 myString]);

[myString appendString:@" - REVISED"];

NSLog(@"after  appendByString: [o2 myString] = %@",[o2 myString]);


And:
    
// Object_2.m
...

NSString *myString;

...

- (void)setString:(NSString *) theString {
  myString = theString;
}

- (NSString *)myString {
  return myString;
}



The log output will be:
    
[...] before appendByString: [o2 myString] = Object_1's Mutable String
[...] after  appendByString: [o2 myString] = Object_1's Mutable String - REVISED


Aside from the normal confusions that can arise from passing pointers around instead of values
(not that this is a bad thing, just that it can get confusing, especially when you're new to
c/Obj-C) the above code has another not-so-obvious gotcha. 

Object_1's     myString is *mutable*, but Object_2's     myString is *im*mutable. 
Clearly though, as shown in the log output, Object_2's     myString appears to have changed 
when Object_1 changed its own     myString via the     appendByString: method.

* **An aside**: You may wonder why the above code works at all, or why you at least don't get a warning 
from the compiler that Object_2's     setString wants an NSString, not the NSMutableString that Object_1 is sending it. 
What's happening here is that     *myString is simply a pointer to an object and the pointer 
that Object_1 passes to Object_2's     setString method works because it's an NSMutableString 
type pointer and NSMutableString is *a kind of* NSString (by virtue of it being an NSString subclass).
Put another way, when the compiler sees a method parameter definition like     setString:(NSString *) theString,
when it checks     theString it doesn't verify that it *is* an NSString, only that it's *a kind of* NSString. 

So, what you maybe thought was an immutable string set by Object_2 ends up being a mutable string set by Object_1.
Trying to debug a problem like this can be a real pain. You're certainly not going to track it down using NSLog calls in Object_2's code
and in a real-life application by the time the symptom surfaces, the actual problem could have happened minutes or hours ago.
So the easiest thing to do is to avoid the problem in the first place.

To get around it, you need to have Object_2's     setString method create a **defensive copy**: 

    
// Object_2.m
...

NSString *myString;

...

- (void)setString:(NSString *) theString {
  // myString = theString;  <-- old code
  myString = [theString copy];
}

...



... and the log output will now be:
    
[...] before appendByString: [o2 myString] = Object_1's Mutable String
[...] after  appendByString: [o2 myString] = Object_1's Mutable String


This new method *defends* against unintended changes to Object_2's     myString
by making a *copy* of     theString and pointing to that instead of using     theString 
(which is really a pointer pointing to Object_1's     myString). (By the way, the NSString     copy method, when 
given a NSMutableString instance returns an NSString.)

The revised code isn't ideal, though. What happens to the string that Object_2's     myString *was* 
pointing to before the     setString method was invoked? It's gone to one of those  
"you can't get there from here" places -- i.e., there's a memory leak. Changing it to the following takes care of that:

    
- (void)setString:(NSString *) theString {
  [myString release];
  myString = [theString copy];
}


--PaulPomeroy

----
**Comments (and more examples):**
----
It's nice when you can put a name to an already familiar face, much thanks!

I think the description works really well but I don't think the code is as clear as it could be. Why Object_1 and Object_2? Odd object naming in teaching-code is just evil.

Here's how I would've started...
    
- (void)awakeFromNib
{
NSMutableString *mutableString = [NSMutableString stringWithString:@"Mutable String"];
DefensiveObject *dObject = [[DefensiveObject alloc] init]; // DefensiveOwner/DefensiveContainer/something_descriptive_without_these_please? :p

      NSLog(@"mutableString = %@", mutableString);
      [dObject setString:mutableString];

      [mutableString appendString:@" - REVISED"];

      NSLog(@"[dObject string] = %@", [dObject string]); // -string and -stringValue are standard, -myString not so much ;)

      NSLog(@"dObject string] class] = %@", [[dObject string] class]); // yes/no?

[dObject release];
}

*Maybe it's an object oriented tip of the hat to Dr. Seuss and the infamous Thing 1 and Thing 2? Honestly, I don't know why I used those names...  Here it is only Tuesday and I've already done something "just evil!" *;)  -Paul

*It took a while to decide on evil over mean. In the end I figured I could do big hand quotes while asking to see your "laser" if you took it the wrong way. ;)*

----

The terminology may not show up in [[ObjC, but the concept is there. It is standard practice to write accessors using -copy when you are dealing with immutable objects, like so:
    
- (void)setFoo:(NSString *)	newFoo {
   [foo release];
   foo = [newFoo copy]; // foo is an immutable string, considered as just some data
}

- (void)setBar:(NSMutableString *)newBar {
   [bar release];
   bar = [newBar retain]; // bar is a mutable string, considered as a pointer to an object where we want to track changes
}

The whole mutable/immutable distinction makes this technique very useful. Calling -copy on an immutable object essentially does nothing, it just retains the object and returns self. Since it's immutable, this is indistinguishable from a "real" copy. This kind of set method is very much standard ObjC when dealing with immutable instance variables.

----

It's also standard to return a copy of an object in a "get" accessor method under some circumstances.  This is for the same reason; if you return a mutable string, for example, it can be altered.

    
- (id)foo
{
    return foo copy] autorelease];
}


What you're calling "defensive copies" is the standard accessor pattern when 1) you feel like being careful at the expense of efficiency and 2) the accessed object is thought of as an attribute of the containing object.  This pattern is inappropriate when the accessed object has more independence from the containing object - it may be expected that changes to the accessed object should change how the original containing object behaves.

-[[KenFerry

*Just to be clear, the term "defensive copy" isn't mine. The first reference to it by that name that I'm aware of is in the book "Effective Java" (an excellent OO concepts book by Joshua Bloch, 2001) although I'm sure the concept was around way before this. -Paul*

----

Here's something I'm not quite "getting" about all this. When the following method is invoked the *first* time,
    
NSString *foo;

...

- (void)setFoo:(NSString *)	newFoo {
   [foo release];
   foo = [newFoo copy]; // foo is an immutable string, considered as just some data
}

why isn't the code dying when it does the     [foo release]? It looks like     [newFoo copy] does a retain, but where is     foo getting retained before     setFoo: runs the first time?


----

The first time through,     foo is nil. So     [foo release] just sends a release message to nil; i.e. it's a noop.

----

Instance variables are guaranteed to be initialized to nil, 0, etc. The same is true of global variables. It is *not* true of local variables, so be careful.

*Which is why making it explicit, as in     NSString *foo = nil; is a good habit to get into.*

----
You can get a bit more efficiency by using f-no-nil-receivers (I forget what it's called in build settings) though, if you make sure that you never message nil.

----

You can get a **lot** more efficiency by writing in assembly ;) In any case, you can just check first:

    if (nil != foo) [foo release];

**No, for the mother of God no, don't do this. Sending a message without checking for nil is one of the idioms of Objective-C.**

----

If you want to take a look at all the standard accessor patterns (as well as save yourself some typing), grab a copy of accessorizer from http://www.kevincallahan.org/software/accessorizer.html
----

*N.B. In the code below, the variable mind should not be released before newMind is retained, in case they reference the same object.*

----
What about arrays? Am I correct in assuming that code like the following simply copies the pointers into a new array?

    
- (NSMutableArray *)mind { return mind copy] autorelease]; }


- (void)setMind:([[NSMutableArray *)newMind {
    if (mind != newMind) {
       [mind release];
       mind = [newMind copy];
   }
}
Can't objects at either end of a     mind or     setMind: message, in a manner of speaking, start messing with each other's minds? 

If so, shouldn't there be a *deep copy* method built in to NSArray?

----

This topic is regularly beaten to death. See [http://www.cocoabuilder.com/archive/message/2004/6/3/108736]

----

RobRix and I both hold that the PerlLanguage means of access protection and defensive copying is best -- you don't mess with other people's stuff because it's impolite.  See parallels with the LawOfDemeter ( http://www.c2.com/cgi/wiki?LawOfDemeter ). -- JoeOsborn

----

Moved this discussion to here (DefensiveCopies) so we can link to the concept by naming it (see http://www.usemod.com/cgi-bin/mb.pl?PatternLanguage ).http://jamtangankanan.blogspot.com/

