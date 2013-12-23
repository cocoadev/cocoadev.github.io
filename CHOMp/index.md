---
layout: page
title: CHOMp
---



CHOMP stands for Cocoa Higher-Order Messaging Platform. It's an implementation of HigherOrderMessaging that aims to be simple and easy to extend. It provides basic iteration over message targets and arguments, as well as a couple of non-iteration utility HOMs.

I wrote a HOM implementation as part of my latest project because I was getting tired of writing NSEnumerator loops, and the one HOM implementation that seemed to be really polished, MPWFoundation, was part of a much larger framework that I didn't want to deal with. CHOMP is compact and relies on nothing outside of Foundation.

CHOMP is currently hosted on Sourceforge at http://sourceforge.net/projects/chomp-mac . You can download the source code from http://prdownloads.sourceforge.net/chomp-mac/Chomp.0.1.tgz?download or grab it from CVS. If you get it via CVS, make sure to get the Chomp module, not the chomp project (capital C). The latter was created during a mistaken checkin.

I'd love to hear your feedback about it. I hope someone finds it useful.

-- MikeAsh

RobRix applauds. I'm very much in favour of advancing the state of the art-- so good stuff, Mike. You might consider file releases just for convenience, though; there's a fair number of people who aren't terribly interested in using CVS.

Thanks Rob! I plan on doing a file release sometime soon. I know CVS can be really inconvenient when you just want to see what something is about. I'll be sure to post here once I get that together. -- MikeAsh

Update: I made a file release so nobody is forced to deal with CVS. -- MikeAsh.

----CHOMp looks very clean, but it�s missing a select: HOM. -- JensAyton

----
This is because select: isn't really possible to implement properly, unfortunately. This is explained in the HACKING file:

*The classic filter messages, -select and -reject, cannot be implemented in a simple, portable manner because the messages given to them typically return BOOL, but the HOM needs to return an object. This could be at least partially rectified by writing filter messages which only act on mutable containers and don't return anything, but this is less than ideal.*

If anybody has advice on how to get around these limitations in a decent manner, I'd love to hear from you.

----
Return an NSNumber instead?  --K

----
Let's say you had a     - (BOOL) isSimilarTo:(id) a andTo:(id) b method. If CHOMp had -select, you'd like it to work like this:

    
NSArray* similarThings = array select] isSimilarTo:a andto:b];


... except it doesn't work, because it returns BOOL and you want id instead. The compiler says, [cite] "Ach! Run, Hans, run!" [/cite], and you're back to square one.

There is no way to do this with BOOL-returning messages, which are the most interesting ones. You'd have to write your own id-returning ones, and return [[NSNumbers. If this is what you suggest, it'd work, but it'd be not as cool as the clean sample above.

 -- EmanueleVulcano aka millenomi

When you already have -filtered(Array/Set)UsingPredicate:, why bother? - Harv.

It's cool, yes, but it doesn't do arbitrary messages. (NSPredicate is fine for 90% of the filtering we need to do, though.) -- EmanueleVulcano aka millenomi

----

Actually it does do some arbitrary messages. (see below)  Anyway, if that takes care of 90% of the cases, is there really a compelling need for filtering with arbitrary messages?  Or are 0/1 argument methods enough to get by?  - Harv.

----
I'm pretty sure the predicate methods couldn't do something like this:

    
array select] hasPrefix:[otherArray each;


Or this:

    
array select] canBeConvertedToEncoding: NSISOLatin1StringEncoding];


Of course there are even more complicated things than this which HOM cannot do, which is why we need anonymous functions a.k.a. [[CodeBlock<nowiki/>s instead. -- MikeAsh

----

"need" huh?  Need for what?  What is the programming problem this solves?  Got an example in a language that supports this style that we can't easily pick apart?

I haven't tried but I believe NSComparisonPredicate's customSelector could be used to implement the snippets above.

So does that take care of 90% of the remaining 10%? :-) - Harv.

----

Whether or not this is a matter of *need*, having CodeBlock<nowiki/>s would be undeniably convenient and useful, since it elevates not just messages to a higher order, but arbitrary code. Function pointers can do a lot that CodeBlock<nowiki/>s are used for, but at the cost of inline declaration, and the locality of declaration is one of the things that makes CodeBlock<nowiki/>s and HOM so useful for one-off sorting, filtering, etc. -- RobRix

----
The programming problem that this solves is one of being able to add new control constructs to the language as a programmer, without having to alter the compiler. Rather than show examples in other languages, I'm going to pretend that blocks exist in ObjC using the standard {} syntax, with parameters being indicated SmallTalk-style using the pipe symbol, and illustrate a couple of examples.

First, here's how you could implement Apple's fancy new for loop without having to wait five years for Apple to get off its butt and add it:

    
// new 10.5 syntax:
for(id obj in array) {
   ... code here for each obj ...
}

// with blocks, must implement a small, simple category on NSArray to implement the     do: method
[array do:{ obj |
   ... code here for each obj ...
}];


Next, here's the filter I showed above using HOM, selecting only those strings which can be encoded in Latin1:

    
newArray = [array select:{ obj |
   return [obj canBeConvertedToEncoding: NSISOLatin1StringEncoding];
}];


But it's not all just about arrays and filters. You can do more. For example, let's say you want to open a file and manipulate it, ensuring that it gets closed even if the code throws an exception. Instead of writing a bunch of     @try/@finally code every time you want to use this pattern, you just write a method on NSFileHandle and then do:

    
[fileHandle executeAndClose:{
   NSData *data;
   while((data = [fileHandle availableData]))
      // process data, maybe throw an exception
}];


Or perhaps you've discovered that in most places you use this idiom, you're really just processing a file on disk, and you're processing it line by line. So you write a new method on NSFileHandle:

    
[NSFileHandle openFile:@"/path/to/some/text/file" forEachLine:{ line |
   [self printLine:line];
   [self processLine:line];
}];


This is just a small sampling of what you can do with CodeBlock<nowiki/>s. Do we *need* them? Well, obviously not. Technically any feature which is not critical to the Turing-completeness of the language is not really necessary, and its lack can be worked around. Obviously ObjectiveC programmers have gotten along without such constructs for a long time while producing a lot of high-quality software so it clearly isn't strictly necessary, but I think it would be a nice feature and it would give us a lot of advantages.

One further note: true blocks are anonymous closures who continue to be valid even after the function which contains them returns. This is a pretty big change to the language and would require a lot of compiler work. However, it is possible to get 90% of the benefit for 10% of the work simply be fixing gcc's nested function support. Gcc supports nested functions, and it is possible to use that support along with some macrology to create a blocks syntax, albeit not exactly as nice as the one above, and one in which blocks do not remain valid after the return of the enclosing function. However, gcc nested functions require an executable stack and as such do not work on Mac OS X anymore. This is an efficiency thing and it certainly could be fixed not to require an executable stack, which would then allow us to add support for CodeBlock<nowiki/>s ourselves. If any Apple people are reading, this gcc problem has been logged as rdar://4450943. -- MikeAsh

