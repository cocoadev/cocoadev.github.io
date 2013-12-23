---
layout: page
title: StringManipulation
---



Ok, is it just me or does Cocoa utterly blow at slicing up strings?

Does anyone have any suggestions on ways to tokenize a string? 

I'd like to be able to just take a string... say a url... and dice it.. find the first token "://" slice to that point, then find the next token "." and so on... until i get the data i want.

Just feels like Cocoa's string manipulation is built around creating a complete crapload of temporary strings and scanners and just doesn't feel right at all. 

----
You reject scanners but don't explain why; General/NSScanner would do a pretty decent job here. Otherwise look into a regex library.

----
Ok, I'll look into them a bit more. Maybe I'm missing something fundamental. I was hoping to get some sort of "hey, try this" where "this" is some other class that totally rocks. lol :-P

----
As you wish: hey, try General/NSScanner.

----

If your problem domain really is General/URLs, NSURL and General/NSString have lots of methods for slicing them up. For the general case, General/NSScanner isn't bad:

The site wasn't accepting changes on July 3, 2006,  but I wrote an example of solving your problem, and posted it on my site: http://turbozen.com/sourceCode/cocoafragments/nsscannerskipadditions.html The key lines are:
     if([scanner scanUpToSeparatorAndSkip: @"://" intoString: &proto] &&
    [scanner scanUpToSeparatorAndSkip: @"." intoString: &prefix]) {  Can't get much simpler and cleaner than that!

Beyond that, http://boost.org has a nice regular-expression package that returns arrays of subpattern matches, and beyond that, boost has a full context-free grammar description language package, called "spirit", that allows for extremely complex and flexible parsing. http://boost.org is a free C++ site, but you can intermix Objective-C and C++, using .mm files which do both, to connect the C++ subsystems to your Objective-C program.

-- General/DavidPhillipOster

----
Uh - stay away from boost::spirit unless you really *love* long compile times. 

-- General/RobertBlum

----

And if you want a very light-weight regex wrapper, try General/CSRegex, which has no external dependencies and is just two files to drop into your project. -- General/WAHa

----
I've been using General/NSMutableString quite a lot, it has a buttload of useful things to it, and it's the only thing I need (up until now). 

-- Matthias
