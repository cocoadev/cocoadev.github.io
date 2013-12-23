---
layout: page
title: HowToSetClassVariablesAtStartUp
---

I've looked in many places trying to find the answer to this question. I know it is a simple question, but I just can't find the answer. Forgive me if it has already been answered.
I have classes that have their own instance variables. How can I set the values of these variables at start up?

----

Terminology: do you mean class variables, or do you mean instance variables? "classes that have their own instance variables" doesn't make sense. Do you mean "startup" as in when the app starts up, or do you mean when an instance is instantiated? I'm assuming here you do mean class variables, and app startup. If you meant otherwise, please say so - precision counts when asking questions.

A class variable is one that is set per class, and exists even if there are no instances. The easiest way to implement these is using a static variable, e.g:

    

static NSString* s_myString = @"Hello, World!";



As you can see this gets initialised at startup automatically - you don't need to do anything. That works for all simple static variables, but wouldn't for an object (except literal strings, which as shown, are an exception). There, the solution I usually use is lazy instantiation, so if you had a singleton class you could do something like this:

    

+ (id)  MySingleton
{
    static s_single = nil;

    if ( s_single == nil )
        s_single = [[MyClass alloc] init];

    return s_single;
}



Thus first time it's called, s_single is nil, so the object gets intantiated. Subsequent calls return the same object, because it's a static var. This is equivalent to initialising at startup time, in a way, because the object is made only when it's first used. If it never gets used, why make it at all? --GrahamCox

----

I would recommend reading Apple's Objective-C primer. This *is* a very basic question. To answer, however, you're looking for the -init method. You don't set **instance** variables at startup, you initialize them for each instance when the instance is initialized. If it sounds obvious, that's because it is. Again, you need to read the basics (Apple's primer and a good Objective-C book are the best places to learn).

If you need to set **class** variables, the +initialize class method is the place to go. You need to know the difference between the two, however, or you will have no end of confusion and frustration. By "the two" I mean class versus instance variables.

