---
layout: page
title: AndAndOrEvaluationOrder
---

This is really a C question in the context of gcc3.3.  If I have something like:
    
 if (a || b)

Will evaluation stop on A being true, and is there any guarantee that A will be evaluated before B? 

----

On GCC v3.3 it appears the evaluation **does** stop on A being true; and A is evaluated before B. However, I would strongly advise against making your code rely on these two "facts." -- RyanBates

For sure -- I wasn't going to **rely** on any specific behavior from GCC; this was an optimization question.  Meaning, should I have two separate ifs, if one of the conditions is almost always true and the other takes a long time to evaluate.  A little bit of lazy evaluation could really speed things up for me.

*My apologies for the wrong advice. I don't know what I was thinking when I posted the above. Those below are right, it is guaranteed to always work this way. -- RyanBates*

----

This is referred to as short circuiting or minimal evaluation, and to the best of my knowledge, has been highly reliable in languages such as C and Java.

----

This is *very* common, and can always be relied upon. If this doesn't work, it's not the code that's broken, it's the compiler.

----
Indeed this rule is pretty much set in stone, and there's much code along the lines of
    
 if(pointer && pointer->value)
    ...

Also, the argument against a     ^^ operator is, that short circuit evaluation does not make sense for xor, i.e. it is the short circuit-nature of     && and     || that make them special (compared to     & and     |), not that they work with booleans instead of bits.
----
Also, C almost has an xor operator already, the != operator. The difference is that != doesn't make all non-zero values equivalent. That can be fixed by using !, which will limit the responses to be booleans. To do a logical     a xor b, do     !a != !b.
----
Ehm... and even if we remove the minus operator, we almost have a minus operator, just use addition and unary minus... and the same can be said for     for (where we have     while) etc. etc. -- if you look at it like this, 90% of the language is just shorter syntax for something already there -- you wouldn't even need     ||, because     a || b is also just     !(!a && !b) :)

----

Really, you don't need much more than NOR and NOT to be able to do any logic (if my memory serves me right). Syntax sugar is bliss, without it we would program in languages such as Brainf**k (which has eight operators, and no variables, just a stack). --TheoHultberg/Iconara

----

Apple has used an example like this in the documentation for NSFileManager since OS X beta. 

    
 NSArray *subpaths;
 
 BOOL isDir;
 
 NSString *fontPath = @"/System/Library/Fonts";
 
 NSFileManager *manager = [NSFileManager defaultManager];
 
 if ([manager fileExistsAtPath:fontPath isDirectory:&isDir] && isDir)
 
     subpaths = [manager subpathsAtPath:fontPath];


--zootbobbalu

That example is interesting, but it doesn't rely on *isDir* to be set to something reasonable if the first method returns false.  Anyhoo... if short circuit evaluation can be relied on, when is it stylistically appropriate to nest conditionals?

----

It certainly can be relied upon -- I'm not sure what you mean with the question about when it's appropriate, but with code like this:
    
 for(; cur; cur = cur->next)
 {
    if(cur->someField == whatWeLookFor)
       break;
 }

I think it is better to write it as:
    
 while(cur && cur->someField != whatWeLookFor)
    cur = cur->next;

While there are probably exceptions, I'd say the sheer amount of lines in an implementation contribute a lot to the complexity of that implementation.

So I'd always write:
    
 if(foo && foo->bar && foo->bar->fud)
    ...

Over 3 nested if's.

--AllanOdgaard

*Hmmm... I like symmetry personally... so I would either do two, or four, and for any other number nest... unless I'm doing bool vars only, and then a list is cool.  Any other opinions on the matter?  (This is a readability question, not a design question)*

If I have a lot of tests, but they must all be combined, I tend to wrap the line e.g.:
    
 if(min_x <= x && x < max_x &&
    min_y <= y && y < max_y &&
    ...
   )
    ...

But I'm not really to happy with this, though it's better than nesting if's IMHO. Another solution, which I also use, is to combine some tests in functions, e.g.:
    
 if(in_range(min_x, x, max_x) && in_range(min_y, y, max_y))
    ...


--AllanOdgaard

