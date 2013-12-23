---
layout: page
title: URLEncoding
---

How can I encode an General/NSString in the correct form to send it as an argument in an HTTP POST operation?

The following Core Foundation function works just fine:

http://developer.apple.com/documentation/General/CoreFoundation/Reference/General/CFURLRef/Reference/function_group_3.html#//apple_ref/c/func/General/CFURLCreateStringByAddingPercentEscapes

for example:
    
General/NSString *myMessage = @"Some Text With+Some&Characters+that would get lost";
General/NSString *legalCharactersThatIWantEncodedAnyway = @"+&";
General/NSString *encodedMessage = General/CFURLCreateStringByAddingPercentEscapes (0, myMessage, 0, legalCharactersThatIWantEncodedAnyway, kCFStringEncodingUTF8);


----

Or use General/NSString's     stringByAddingPercentEscapesUsingEncoding: method (10.3 or later).
