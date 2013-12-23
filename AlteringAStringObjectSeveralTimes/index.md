---
layout: page
title: AlteringAStringObjectSeveralTimes
---

I have an NSString that I modify several times (eg. trim, replace occurrences of string etc.). Is it assumed that I will end up with the exact number of autoreleased strings that is equal to the number of modifications? What if I use a huge string? 
Is there a better solution?

    
AGRegex *regex = [[AGRegex alloc] initWithPattern:@"\\<[a-zA-Z0-9=\"_ /]*\\>"
    options:AGRegexCaseInsensitive];
NSString *result=[regex replaceWithString:@" " inString:testString];
[regex release];
regex=[[AGRegex alloc] initWithPattern:@" +" options:AGRegexCaseInsensitive];
result=[regex replaceWithString:@" " inString:result];
[regex release];
result=[result stringByTrimmingCharactersInSet:[NSCharacterSet
    whitespaceAndNewlineCharacterSet]];


Will this code above produce memory leaks? (I think it won't)

----

It won't. You release regex for each     alloc, and everything else is autoreleased (or should be). You might want to use a temp string in line 4, rather than altering result in place, but that's mostly a style thing. Or make result a mutable string. I think AGRegex has NSMutableString category methods.

