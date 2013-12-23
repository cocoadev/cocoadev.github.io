---
layout: page
title: CodingAssistant
---

I would like to create an IDE with the same support offered by IDEA, Eclipse or Visual Assist. This means auto inserting closing brackets, quotes, braces, parenthesis etc. Intelligent indenting (extra indent after { or an if), inserting extra quotes when splitting strings and so on.

The main problem with ObjectiveC is the pre-processor. And thinking about it, I don't think it can easily be ignored. E.g. I have the following header named Debug.h:

    
#ifndef NDEBUG
#define D(flag, code) \ 
   if(flag.isEnabled()) \ 
   { \ 
      code \ 
   }
#else
#define D(flag, code) ;
#endif


So I would write code like this:

    
#include "Debug.h"

void someFunction ()
{
   D(DBF_XYZ, printf("Did enter someFunction()\n");)
   ...
}


If we did a simply heuristic like lines not ending with ; should lead to an extra indent, then the previous example would break that -- we really need to know exactly what the code expands to -- but this is actually not known before gcc is started, because given the -DNDEBUG paramter it would expand to one thing, but leaving out the parameter, would yield another result. Granted most macros should be defined in a way that wouldn't change the semantic of the code, but there are macros like "#ifdef MACOS_10_2_OR_HIGHER" (typing from memory), where e.g. some functions from a header file would be left out, without this define, thus causing code completion to fail for these bits.

I guess I haven't really asked any questions, but I would like some input if anybody has given this problem any thoughts. I will probably end up with first running a pre-processor, then a tokenizer and finally a simple parser, each time the code changes, trying to cache as much as possible, and also allow the user to set some "global defines" in the IDE (which would be used by my pre-processor), and let it be his responsible to keep these in synch with the gcc options (until my IDE will also launch gcc with proper options or similar).

Another concern I have is that I'll hardcode the entire thing to ObjectiveC++ -- similar to how IDEA only supports JAVA -- it would be much nicer with a generic IDE where one could get the same features for a dozen different languages, but it seems to be very limited how far one can get by configuring all this through normal regular expressions.

I am however prepared to offer the ability for plug-ins and perhaps even accept BNF notation and grammars to descriebe a language (and create tokenizer/parser from these), but I wonder if this system will be general enough, or if I am just wasting my time trying to make it general...

Another question is, if hardcoded for ObjectiveC++, what about ANSI-C (C90, C99), K&R notation and so on... would it be okay to assume that we are all moving toward ISO C++ (with ObjectiveC support), or do people still use the older notations (or the C99 specific features which are not present in ISO C++).

----

The right thing to do is to simply write your     #defines in such a way that you end them with semicolons like any other statement. If your     #define expands to a single statement, write it like this:
    
#define MyDefine(x) SomeOtherFunction(42, x, 101) // no semicolon at the end

For multiline     #defines, you can wrap it in a do-while loop:
    
#define MyDefine(x)
do {
   SomeFunction(x);
   SomeOtherFunction(x, 100);
} while(0) // doesn't loop, but lets you have a semicolon

This is the standard idiom for writing multi-line     #defines. That way you can still do things like:
    
while(condition)
   MyDefine(3);

if(condition)
   MyDefine(4);

And in general, the     #define acts like a function. This doesn't answer any of the rest of your post, of course, but it's a helpful thing to know.

(On another note, inserting backslashes at the end of the lines in my multiline #define resulted in the entire thing showing up on one line when it was turned into HTML. Anybody know why, and how I can do it correctly?)

----

Stuff like this (auto-indenting, pasting in templates and stuff) has been in emacs for years.  Depending on your aversion to lisp, you can see how they do it.

