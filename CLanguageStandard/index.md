---
layout: page
title: CLanguageStandard
---

XCode lets you set which C Language Stardard to compile with in XCode's target build settings (and probably PB does too, but I haven't checked).

To set which C standard to use, go to your build targets, select a build target and click Apple-i. This will popup the inspector for the selected build target. Under the build tab in the inspector there should be a setting for "C Language Standard".

The possible values are ANSI-C, C89, C99, GNU89, GNU99, Compiler Default. I believe the compiler default for gcc at the moment is GNU89, but this will change to GNU99 sometime in the near future.

Note: If you are running into an error like "...used outside C99 mode" then you are probably using some C feature that isn't supported in the default setting (such as variable declarations in for loops).

The following extract taken from http://www.faqs.org/docs/learnc/x134.html by Ciaran O'Riordan summarizes the differences between the standards.


----

1.5. Who defines Valid C?
For you, the programmer, "valid C" is defined by the compiler. There are many dialects of C in existence, thankfully they are all very similar. There are also other languages that are based on C such as Objective C and C++. These languages are very like C in there appearance but there usages is quite different. GCC understands many dialects of C as well as many other languages (including Objective C and C++).

1.5.1. "K&R" C
C was created by Dennis Ritchie between 1969 and 1973. In 1978 Dennis Ritchie along with Brian Kernighan published an excellent C tutorial "The C programming language". This was the first formal definition of the language. Being the original C dialect it is sometimes called Traditional C. Unfortunately the book left many aspects of the language undefined, this meant that people writing compilers had to make decisions as to how to handle these aspects. The result was that a piece of code would behave differently depending on what compiler was used. This dialect is no longer used, GCC supports it only for compiling very old programs. We mention it here purely for historical purposes.

1.5.2. ISO C
In 1983 the American National Standards Institute (ANSI) set up a committee to draw up a more exact standard and fix a few shortcomings they saw in the language. In 1989 they finalised this standard which was accepted by the International Standards Organisation (ISO). This new dialect became known as "C89". It is also called "ISO C" or "ANSI C". GCC is one of the most conforming compilers available.

1.5.3. C99
The ANSI C committee meets infrequently to update the standard. The latest updated standard was released in 1999 and is known as "C99". Few compilers fully support C99 yet; making changes to one of the most important pieces of software to an operating system takes time. GCC's C99 support is mostly complete (at the time of this writing) but the developers are working on it.

1.5.4. GNU C
GNU C is most similar to C89 but has a lot of the new features of C99 added and a few other extensions. These extensions have been added conservatively by the developers as problems are found that C99 doesn't provide good solutions to. GNU C is the default dialect of GCC and is the dialect we will use in this book. We will try our best to point out GNU extensions when we use them but in general, it is better to make full use GNU C. Use of ISO C is limiting your programs to the lowest common denominator and should only be used in special cases.

1.5.5. Choosing a Dialect
If you would like to use a dialect other than the default, you can specify your choice with the -std= switch followed by name of the dialect. The names are: c89, c99, gnu89 and, gnu99. "gnu89" is the current default but "gnu99" will become the default when C99 support is complete. The change will not be very noticeable.

1.5.6. Future Standards
Extensions such as those added by GCC are the main source of inspiration for new ISO C standards. When the ANSI C group see a lot of compilers implementing an extension they review the necessity of that feature and if they decide it would be of benefit they work out a standard way to implement it. Some of GCC's extensions may make it into the next standard, some will not.

