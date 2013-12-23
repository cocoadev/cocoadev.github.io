---
layout: page
title: InputManagerProblems
---

I am trying to create an InputManager for OmniGraffle 3 (don't ask me why at this point). I have made other InputManagers for iChat (specifically iChatExporter, and I worked on iCAR with Adrian Sampon). However, I seem to be having some trouble with this one. I class-dumped that actual OG3 executable from its bundle, and weeded out all of the classes that I didn't need, and of course fixed all of the weird struct things that class-dump puts before non-ObjectiveC classes (such as NSRange, NSPoint, etc).

So I make my new project, add this new header file, and make my lovely classes. One class that I made was a subclass of OG's application delegate (called GraffleApplication, iirc, but we'll just call it that for now :-) ). In the +load method of my main class, I make this new subclass actually pose as GraffleApplication so I can "control" it, for lack of a better term. But for some reason, whenever the projects compilation gets to the linking stage, I get an error about an undefined symbol called .objc_class_name_GraffleApplication, or something along those lines (I'm too lazy to open up PB right now and copy the error directly).

I'm not exactly sure how to go about fixing this problem, especially because I didn't have any problems at all making an InputManager for iChat. In the "master OG header file" that I edited from class-dump, I also put @class directives at the top of it for all of the OG classes that I left defined in the file (since that's what I had to do to make it work for iChat).

Does anybody have any ideas as to why I get this linker error? I also tried using an exports file, but it didn't seem to work either. It was my understanding that with an InputManager, the classes that you try to use from the host application can't actually be resolved until runtime... for obvious reasons, of course.

TIA for any help anybody can provide!

-- MarcWeil

----

Trivial, but...make sure you have something like the following your file
    
@implementation GraffleApplication 
/* blah */
@end


----

It might help to e.g. use     NSClassFromString(@"GraffleApplication") where you need to reference the class, but probably providing an empty implementation will be enough. (And hopefully it won't conflict with the non-empty implementation in the program.) -- RobRix

----

Okay, that *worked* meaning it allowed the thing to be compiled. However, now the objc runtime (as logged in the Console) wants to actually use the implementation defined in the InputManager rather than in OmniGraffle itself, which could certainly be a problem since I don't want to override any other function in OG3 other than the ones that I need. Are there any ways to make sure that normally, control of the implementations I override are still with the program unless I specifically override a function in the class?

Thanks again!

-- MarcWeil

