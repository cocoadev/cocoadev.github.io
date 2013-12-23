---
layout: page
title: NSUInteger
---

From Apple's Leopard overview [http://developer.apple.com/leopard/overview/apptech.html]

*Most applications won't need to make the jump to 64-bit yet. However, there's no reason not to be ready to move an application to 64-bit in the future. To ensure that you are ready, you'll want to be sure that you write your code in a manner that's easier to move to a 64-bit later. The best way to do this is to move away from deprecated APIs, such as QuickDraw, and adopt the use of the new NSInteger and NSUInteger data types that are used throughout the system frameworks in Leopard in place of int and unsigned int. By doing these tasks now, you'll have much less work to do later.*

    
#if __LP64__ || NS_BUILD_32_LIKE_64
typedef long NSInteger;
typedef unsigned long NSUInteger;
#else
typedef int NSInteger;
typedef unsigned int NSUInteger;
#endif


----
Am I the only one who thinks these typedefs are silly and there's no need to use them? In my mind, use one of the types like     uint32_t for when you care about the size of the data type, and use a type like     long for when you don't. I can just barely grasp why Apple feels a need to use them in Cocoa (barely), but I can't see any reason to use them in mine. Thoughts? -- MikeAsh

----
Basically, Apple is introducing the NSInteger/NSUInteger types to ensure that arrays, strings, data, etc. can hold system-appropriate amounts of information. uint32_t is a fixed size (boo) and long is too vaguely-defined for Apple's needs.

----
But how are NSInteger and friends defined? From what I can tell (not having looked through any NDA'd material on them), they're just as ill-defined as int or long, so what's the point?

The main thing that puzzles me is that usually when you get to worrying about integer type sizes and such, it's because you have some specific use in mind. For example, you'd want something big enough to hold a pointer (    intptr_t), the largest available on the platform (    intmax_t), the fastest size (generally     int), the largest "native" on the platform (generally     long), and so forth. So not only does the language already cover a lot of these use cases, but Apple's new types don't give you even the slightest hint as to what their intended use is or how they're expected to change on other platforms. For example, I know just what     intptr_t or     intmax_t will do on any given platform, even if it's something with 36-bit words, 9-bit chars, and tradition for emulating 160-bit integers in the compiler, but I have no idea what NSInteger would be in that situation. It just seems poorly thought-out. Am I missing something here? -- MikeAsh

----

The intent of NSInteger is control over something that otherwise depends on too many variables: compiler, CPU family, specific OS versions, etc. By creating a "new" type that Apple controls, the company doesn't have to worry that some future architecture will define unsigned ints as 124 bits and unsigned longs as 33 bits (which I think is possible under ANSI C.) intptr_t and intmax_t are inappropriate because the former is (paraphrased from the C standard) "an integer type large enough to hold any valid pointer value" while the latter may require non-atomic operations (e.g. on 32-bit Mac OS X, where it is equivalent to int64_t) in order to use it.

If you dig deep into Darwin, you see types defined like u_int32_t, __darwin_intptr_t, time_value_t, and a number of other redundant types. There are also about a hundred and eight different types representing errors in Mac OS X -- NSError, OSErr, OSStatus, kern_return_t, and the venerable int come to mind. Don't worry about NSUInteger, because there's much worse floating around in your /System/Library/Frameworks/ directory. Just go with the flow. :)

----
What format string are we supposed to use for NSInteger and NSUinteger?  The guy hosting one of the Leopard tech talks was really wishy-washy about this, which is worrying.

----

Just cast them to intmax_t and uintmax_t and then use the format specifiers for those (PRIiMAX, PRIxMAX, PRIuMAX, etc.) If using *scanf() remember to scan into an intmax_t or uintmax_t and then convert.

----

Just use the format specifiers Apple suggests: [https://developer.apple.com/documentation/Cocoa/Conceptual/Strings/Articles/formatSpecifiers.html]

Cast NSInteger to     long and NSUInteger to     unsigned long and use %ld or %lu, respectively (or %lx if you want it in hex).

----

I don't get why they didn't call it NSInt (and NSUInt). I don't want to type eight characters when five will do (and it's in keeping with C). I'm all for description but for such a common type, this is just stupid. Anyway, I'm sticking to int and float for the time being. If and when we actually need these, it will only take 2 mins to change with project find in Xcode. In the meantime, int is a heck of a lot more readable and writable (at least for me).

----

    
#define NSInt NSInteger


----
Ugh, use     typedef, this is what it's for. Using a     #define is just asking for trouble.

----

Of course, if you're using NS as a prefix, you're polluting Apple's namespace. Why not just go all-out shorthand and do this:

    
typedef NSUInteger UInt;
typedef NSInteger Int;


Or, you could just use the types that are provided by Apple, and make use of autocomplete if it's really so hard to type.

----

FYI, there is a "tops" script located at /Developer/Extras/64BitConversion/Cocoa64.tops that will help you automatically change from (unsigned)int/long to NS(U)Integer. The Cocoa team suggests you make a copy of your source (since this modifies it in place), and then diff between them to find inappropriate substitutions, since the script is not perfect. The script will also insert #warnings in places where it can't make a definite decision on its own.

----

Obviously I could use a typedef, that's not the point. It's just that I don't see the logic in making developers' lifes harder for no apparent reason. Anyway, it was just a small point, and Xcode does a good job with the new super-inline autocomplete.

----

Mac OS X uses LP64. `int` is always 32-bit. `long` is always pointer-sized. 

Functionally, `NSInteger` is identical to `long`. It's a 32-bit integer 
on 32-bit architectures, and a 64-bit integer on 64-bit architectures. 
If you replace `NSInteger` with `long` in your head, you get LP64. 

NSInteger exists for use in APIs that should be 64-bit integers on 
64-bit architectures, but need to be typed as `int` instead of `long` 
on 32-bit architectures for backwards compatibility (such as C++ name 
mangling and Objective-C @encode). 

----

Not quite! 'long' is not always pointer-sized, it's only always at least 32 bits wide. On Mac OS X it's currently the width of a pointer, but a future port of the system could have 94-bit longs and 150-bit pointers (for example) and it would still be C-conformant.

----
And it could also have 12-bit NSUInteger<nowiki/>s and be conformant, that's the whole problem, there's nothing that says just what these things actually mean. If you really want an integer that's the size of a pointer there's already a standard type for that,     intptr_t, and other such typedefs for other desired semantics.

