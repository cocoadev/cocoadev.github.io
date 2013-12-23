---
layout: page
title: ConfusionInTheGlossary
---



Is everybody certain that DynamicMethodDispatch really *is* the same thing as MultipleDispatch? It seems to me that DynamicMethodDispatch would actually be much closer related to DynamicBinding (going by the phrase "DynamicMethodDispatch", of course), but this could very well be mistaken.

In any case, I'm just hoping for a little more clarity on that issue.

-- RobRix

----

I agree with Rob on this one. The entry for MultipleDispatch is inaccurate. I'm still a newbie here (less than an hour onto this site) and still discovering the etiquette of Wiki's as I go, so I'm not ready to delete the existing text, but my understanding of MultipleDispatch is this: "use of more than one parameter in  determining which concrete function to branch to when a message is sent at runtime". The normal method dispatch mechanism of Objective C, C++ Java, etc, is to branch to the method implementation based solely on the class of the *reciever* of the message. Some esoteric languages (like Apple's ill-fated DyLan) allow for method resolution based on **all** the arguments to a function. (I think CLOS might have this as well).

-- TimGogolin

----

So, MultipleDispatch actually works more similarly to function overloading, or something like that? In theory, I mean.

When I wrote the MultipleDispatch page, I was just getting information in quickly, so feel free to edit where you see the need! Anyhow, I don't really know that much about MultipleDispatch, since I got my info from an e-mail conversation :)

-- RobRix

----

Ummmm. Kind of. Except that "function overloading" (in the C++ sense) usually refers to *compile time* decisions, whereas "multiple dispatch" refers to *run time* decisions. 

Example:
Lets assume a "virtual" method in C++, myObject.fooWithAandB(myA, myB); or an ordinary ObjectiveCee method call [myObject fooWithA: myA andB: myB]; In either of these cases, there is a compile time decision made on how to set up the function call (C++ uses the types of the parameters, Obj-C uses the message name only which is why people tend to use longer message names in Obj-C than C++). However at run time, the only factor that matters is the class of "myObject". The type of myA and myB are ignored for this decision. In DyLan speak (the only MultipleDispatch language I have even a passing familiarity with) there is no reason to specially elevate one param above the rest; in fact there is no reason to seperate them syntactically at all! In Dylan the call would look like foo(myObject, myA, myB), which sorts of busts the syntactical separation of myObject as a special "actor" on A and B, because all three are coequal partners in this function call. Its a tough thing to wrap your mind around (Caveat: I've never actually programmed with it). But it makes sense theoretically, because there really is nothing special about the receiver object; its just another parameter to an ordinary "C" function that (in "mainstream" languages) happens to be syntactically passed differently than the other params.

I'll correct the Glossary entry after I think about how to express what I know a little more clearly.

-- TimGogolin

----

Hey Tim,

As far as wiki etiquette goes, if you see something that you know for sure is inaccurate or misleading, make the change! :)  It is encouraged and appreciated.

-- StevenFrank

----

Sorry: I forgot to mention the runtime caveat (it was assumed by me, but I should have stated it...). So, if you had function overloading decisions made at runtime, it would, more or less, be a multiple dispatch-type situation, yes?

In any case, it's good to have you with us, Tim!

-- RobRix

----

Yes (as far as I understand it).

-- TimGogolin

----

Excellent!

-- RobRix

