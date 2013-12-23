---
layout: page
title: ForceRecursiveFrameworkLink
---

How can I force an application that uses my own framework to use other frameworks needed by it? I'm looking for a way to make it automatically, if that's possible. I mean, I know I can include the requiered frameworks  by hand but wouldn't it be nice if the application didn't know about the components required by my framework?!?

----

Are you sure you're talking about *recursion*? Do you have a situation where Framework A uses Framework B, which links to Framework A in turn? Or do you just want to make sure that any application which links to Framework A will also, automatically and transparently, link to Framework B?

If it's the second scenario, it's easy:

**Compile Time:** If you're worried about giving application-writers compile-time access to headers in Framework B without having to explicitly link to it, link to it in Framework A and chuck the following line into the public header for Framework A...

    #import <B/B.h> // (where B is the name of your subsidiary framework, obviously.

**Runtime:** If Framework B is part of the MacOS, then the required dynamic linking should happen anyway, whenever the application is launched. If both A and B are frameworks you have written, embed Framework B in Framework A (see EmbeddingFrameworksInApplications for details).

--ToM

You can also load frameworks dynamically at runtime, just as you load other bundles. You can have your framework A load framework B at runtime. If framework B contains classes that are subclasses or conform to protocols in framework A or your application, then your application will never know that they came from somewhere else, and you wouldn't have to include the headers at compile time.

--TheoHultberg/Iconara

Actually I wanted to know the details of how to do it. I'm writting a framework, A, which uses the OpenGl framework. However, when I use  framework A in my application, I get an error message like this: _glBegin defined in indirectly referenced dynamic library. So, after all, I need to include OpenGl framework to my app project. Doesn't frameworks allow to include automatically other frameworks?!? (Sorry about the recursive word.... I knew it wasn't the right one but couldn't think of a better choice ;)

--Jose 

----
Sure, the recursive linking happens automatically. What doesn't happen automatically is when *you* use a symbol in a recursively linked framework. It works fine if one framework uses symbols from another, but this error means that your app is using symbols from a framework which it's only getting indirectly, and that's not allowed. (Because, for example, what happens if the directly linked framework stops linking the indirect one? Kaboom!)

