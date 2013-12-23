---
layout: page
title: DProgrammingLanguage
---

A programming language developed as a successor to both C++ and Java.  It is a fully compiled language, and can be used to write low-level code.  It is also fully garbage collected. D sacrifices C source compatibility, though it is fairly easy to wrap C functions inside D.  The current D compiler is available at http://www.digitalmars.com, and a port to GCC is underway.  --OwenAnderson

Status Update:  The GCC frontend has taken a major leap forward, and is now fairly stable with support for 99% of the the language.  It compiles on Linux and Mac OS X, and generic UNIX support is underway.  Most bugs are now specific implementation errors, and the compiler can be used for development.

Check it out: http://home.earthlink.net/~dvdfrdmn/d/
News: http://www.digitalmars.com/drn-bin/wwwnews?D.gnu

Known Bugs:
Debugging isn't perfect.

Has problems exporting templates on OS X.  Is fixed in Apple's GCC code, but doesn't build against that yet.

Floating point calculations don't meet the D standard's precision requirements yet.

Garbage collection only applied to the data segment on Mac OS X.

Exceptions may cause memory leaks.

The help of anyone with knowledge of GCC, Darwin, or just general compiler knowledge would of course be most appreciated.

--OwenAnderson

Status Update: With the advent of a mostly function OS X compiler, I've started work on interfacing D to Objective-C. Thus far I've gotten the example on introspecting the ObjC runtime from C converted to D, and it runs nicely.  Anyone interested should check out my posts about "Docoa" on the newsgroup.  --OwenAnderson

----
I don't know, I have yet to take any language that claims to add GC to C/C++ seriously. People that feel that C/C++ should have GC capabilities don't understand what the intents of the language are and/or where it is meant to be used. If you forgo C compatibility, you forgo acceptance. C/C++ are the languages that replaced machine code and assembler (actually a HLL to machine code) to implement many other languages and operating systems. If you feel that memory management is hard with C, don't complain and pick another language.

*D is that other language -- it is only compatible with C (not C++) on the link-level.*
----
First off, it's not just C++ with GC added.  It really is its own language that inherits a lot of ideas from C++ and Java.  Secondly, the GC is disableable, and you're free to do memory control manually if you want.  Thirdly, it DOES NOT forgo C compatibility.  C functions can be called directly from within D by declaring them as extern (C).  The only limitation is that D and C cannot share a file.  They are, however, completely and intentionally link compatible.  --OwenAnderson
----
Does it have deterministic destruction of temporary objects?

For example if I do something like:
    
copy(a, a + N, cached_inserter(dst));
copy(b, b + M, cached_inserter(dst));

Here     cached_inserter is a temporary object which wraps for     dst and inserts into the wrapped object only when some cache is full (for speed reasons) *and* when the object is destroyed -- for this reason the first temporary *must* be destroyed *before* the creation of the second temporary, which e.g. C++ guarantees, but normally such guarantees are not given in GC languages.
----
I'm not sure I fully understand your question, but I think the solution is covered in these two language features:

1) Objects created with the 'auto' keyword are automatically destructed when they leave their creation scope.

2) Both the 'new' and 'delete' operators can be overridden.

If those don't cover it, I'm afraid I don't understand the question very well. --OwenAnderson

Hmm... having looked up what deterministic destruction meant in terms of C#, D does support it.  D objects can declare a destructor as ~this(), which the garbage collector will call before deallocating them.  The destructor will be called whether the object is released explicitly (delete operator) or implicitly (no more references), and is guaranteed never to be run more than once.  The destructor for the class's super class is automatically called after the class's destructor ends. --OwenAnderson

*In the example above,     cached_inserter(dst) creates a temporary object (from the class     cached_inserter), this object must be destroyed before executing the second copy (so that the cache is flushed), in D that would probably be guaranteed by writing:*
    
{ // make dummy scope for tmp
   copy(a, a + N, auto new cached_inserter(dst));
} // ensure that the tmp is destroyed

{ // make dummy scope for tmp
   copy(b, b + M, auto new cached_inserter(dst));
} // ensure that the tmp is destroyed

*This of cause is better than other GC languages which doesn't have the auto-feature at all (AFAIK). But although my example above is not strictly RAII, it does cripple the RAII idiom because the intent of this is to *not* make the programmer think about resource management -- the intent of GC is of cause the same, but some resources needs to be disposed immediately, like my cached insert iterator or e.g. an object which locks the printer, a file, the screen, puts up a window for the duration of its life etc.*

Ah.  I don't know that that's guaranteeable within the language by default, but I suspect it could be done by overriding the delete operator (which functions like C++'s).  If you overrode it to make sure the object was destructed immediately rather than waiting for a GC sweep, you could ensure that the resources were released by the time the new object was created.  This is similar to the case of Singletons in D: enforced Singletons don't exist in D per se, but one can create them by overriding new and delete.  --OwenAnderson

*I am not sure what that should help? are you saying I should explicitly call delete? so the example becomes:*
    
Object tmp = new cached_inserter(dst);
copy(a, a + N, tmp);
delete tmp;
...


Right.  I don't know for certain if 'delete' destructs it immediately, or if it waits for the next GC sweep.  If it waits, it's possible to override the 'delete' operator for an object, so you could ensure that it got deallocated immediately.  --OwenAnderson
----
Does it have Standards document yet? ANSI or ISO/IEC?

I don't know of a formal Standards document (i.e. ISO or ANSI), but the working document is at http://www.digitalmars.com/d/index.html.  I suspect a formal document won't be made until the working copy reaches 1.0 (currently 0.82) --OwenAnderson

