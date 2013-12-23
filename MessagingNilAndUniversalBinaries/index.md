---
layout: page
title: MessagingNilAndUniversalBinaries
---



Here's a list that I hope will start a discussion about when messagning nil is ok and when it is not. 


*    if ([possibleNilObject length]) do something;
*    if ([possibleNilObject frame].size.width > 100.0f) do something;


Basically it is ok if you return an object, otherwise the result is undefined. There is a blog entry somewhere that tells you what will happen with other values.

----

It depends on what you mean by "ok".

If you mean "will work on any shipping and predicted Macintosh system", then messaging nil is fine for any message which returns an object or an integer that's 32 bits or smaller. Floats and structs are out.

----
http://developer.apple.com/documentation/MacOSX/Conceptual/universal_binary/universal_binary_tips/chapter_5_section_22.html

An integer less than where sizeof(INT_RET_TYP) <= sizeof(void *). sizeof(void *) may be > 32 bits on some architectures.
----

If you mean "is guaranteed to work by the language spec for all time", then it's only ok for messages which return objects. No integers, no booleans.

----

In other words, to be pedantic, neither of the examples listed originally are right. Instead, they should be:

*    if (possibleNilObject && [possibleNilObject length]) do something;
*    if (possibleNilObject && ([possibleNilObject frame].size.width > 100.0f)) do something;

--EvanSchoenberg

----

I'd be happy to go by the book as Evan illustrates, although in some cases my code is going to get notably longer. Let's say I want to check the value of a possibly nonexistant key-value pair in an NSDictionary. Is there any shorter/more elegant way to do it than:

*    id dval = [someDictionary objectForKey:@"somekey"];
*    if (dval && [dval isKindOfClass:[NSNumber class]] && ([dval intValue] == kSomeConstant) ) do something;

I'm not really very worried that the value isn't an NSNumber, since I created the dictionary at some point and wouldn't be putting in some random object.

----

Well, if you aren't worried that the value might not be an NSNumber, why are you checking it? :-)  If you know that not checking is safe, then it's safe.
    
NSNumber *dNumber = [someDictionary objectForKey:@"somekey"];
if (dNumber && ([dNumber intValue] == kSomeConstant])) {
     // do something
}

