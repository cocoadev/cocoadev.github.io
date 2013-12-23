---
layout: page
title: NSStringIsEmptyString
---

OmniFoundation contains a NSString class method:

    
+ (BOOL)isEmptyString:(NSString *)string;
    // Returns YES if the string is nil or equal to @""
{
    // Note that [string length] == 0 can be false when [string isEqualToString:@""] is true, because these are Unicode strings.
    return string == nil || [string isEqualToString:@""];
}


Can anyone explain how a string with length > 0 can be equal to @"". It doesn't make sense in my universe, unicode strings or not.

----

In general, it's possible for     [a isEqualToString:b] even though     [a length] != [b length] because of things like precomposed/decomposed characters and such. However, you still have to have *some* character for that to apply, and so I agree that this makes no sense. It's also weird that this is a class method, rather than an instance method on NSString (provided by a category).

*It is a class method because if string is nil, then this method will never be called. Instead of     if (string == nil || [string isEmpty]) you can simplify it to     if ([NSString isEmptyString:string]).*

----

I just use ![string length]. Works for @"", works for nil. Jon H.

*It should be noted that, while it works for nil on PPC, and it works for nil on x86, and it will probably work for nil on many future architectures, it is not **guaranteed** to work for nil by the language in the way methods returning objects are guaranteed to work.*

**Not only that, it is actually *not* supposed to work on x86 (according to Apple), so this is pretty dangerous.**

This is incorrect. The only difference between PPC and x86 as far as messages to nil go are messages which return float/double and messages which return small structs. On PPC these messages often (but are *not* guaranteed) return zeros, and sometimes garbage, whereas on x86 they always return garbage. The official line on this behavior is here: http://developer.apple.com/documentation/MacOSX/Conceptual/universal_binary/universal_binary_tips/chapter_5_section_17.html

**Anyway, a string with, say, seven null characters, or     'a', backspace might have a length greater than 0 but be equal to     @"". --JediKnil**

*Presumably a solitary Unicode modifier character would be considered zero length (if not malformed)?*

