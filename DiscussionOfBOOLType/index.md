---
layout: page
title: DiscussionOfBOOLType
---

see also TypeIDDoesNotCastToTypeBOOL, a very important point made by MikeTrent

**BOOL is a scalar type, not an object type**

I have an object that implements a BOOL in the header file.  I assign it a value in the awakeFromNib method.  I have implemented accessor methods for the variable.  When I try to reference it, I get a SIGBUS error:

the variable is declared as:
    BOOL viewReduced;

and the accessor methods are declared:
    -(void)setViewReduced:(BOOL)yesOrNo;
    -(BOOL)isViewReduced;

then, in my awakeFromNib: method I say:
    viewReduced = NO;

Relevant implementation code:

#import "MyView.h"

Now if I so much as say     "NSLog(@"viewReduced = @", viewReduced);
the result is (null).

----

BOOL is not an object type, so your log statement above is incorrect. It should be:*

    
NLog( @"viewReduced = %d", viewReduced);


----

Actually, I have abandoned the approach that involved that variable.  The problem could simply have  been my use of "@" in my NSLog while referring  to a BOOL.  I'm sure that's what it was.

----

%@ means an object, and BOOL is not an object. If you want to log a BOOL, you can use %d, or you can do something like this:
    
NSLog(@"MyBool is %@", myBool ? @"YES" : @"NO");


NSLog() has caused crashes for me from stupid mistakes like that. BTW, what is a BOOL really? It appears to be some type of macro? Why use something other than the C/C++     bool?

----

Command-double-click "BOOL" in Xcode and find out!

The answer is, it's simply a typedef to a type of     char. The reason they don't use     bool is because     bool only appears in C++, not in C, and ObjectiveC is a superset of C; C++ is something else. C99 has a _Bool type, but obviously this was not available when ObjectiveC or Cocoa were first being designed.

*Taken from     <objc/objc.h>:*
    
typedef signed char		BOOL; 
// BOOL is explicitly signed so @encode(BOOL) == "c" rather than "C" 
// even if -funsigned-char is used.


----

**More about BOOL versus bool and Boolean**

I'm very confused as to the difference between BOOL and Boolean in Objective C.  I've been using BOOL, but then, suddenly, I needed to use an NSAppleEventDescriptor method: booleanValue.  And this returns a Boolean.  I can't figure out how to even save such a thing in a variable.

----

Boolean and BOOL are basically the same thing. In straight-C any (integer-type) value that is not zero is true, the type Boolean is just a typedef'ed int or short (not sure which). In ObjectiveC the type BOOL is also an type that can be cast to an int. There are also the values YES and NO, which are just #defines for the values 1 and 0, respectively. True and false exist for Booleans.

So if a method returns a Boolean, and you don't really know what to do with it, check if it's 0, false (NO); otherwise, it's true (nonzero) (YES).

If you don't want to treat Booleans and BOOLs as the same, you can always do this:

    
BOOL returnval = methodReturningBoolean() ? YES : NO;


Which 'converts' a Boolean to a BOOL.

Here is more info: http://cs1313.ou.edu/boolean_data_lesson_2up.pdf (google cache: http://66.102.11.104/search?q=cache:gpRNOUz_BPwJ:cs1313.ou.edu/boolean_data_lesson_2up.pdf)

Personaly, I don't care for the BOOL type, I think it clashes with otherwise nice syntax in ObjectiveC. SEL, BOOL and IMP are all-caps, which is very odd, did Cox think that is was so very hard to type selector, boolean or implementation, written out, and in lower case (to denote that these are primitives, not classes). CAPITALS are for constants, not for types.

-- TheoHultberg/Iconara

----

FYI,     Boolean is a data-type left over from the dawn of the Macintosh. NSAppleEventDescriptor wraps Carbon's     AEDesc type, and the boolean     AEDesc is documented as containing     Boolean.

There are at least three boolean types: BOOL, Boolean, and C's bool. **Only the last is a true boolean type.**

----

    Boolean is typed to to     unsigned char, while     BOOL is typed to     char which means that     sizeof(Boolean) == sizeof(BOOL) == 1. The values of a     Boolean are     TRUE and     FALSE, those of a     BOOL are     YES and     NO,, and those of a     bool (the ISO C type) are     true and     false. In each case they are defines for 1 and 0, respectively.

----

**Is type BOOL safe for addition?**

    YES and     NO are defined as     (BOOL)1 and     (BOOL)0. Is it safe to assume that these definitions will always be the same. The reason I ask is I was wondering if the following statement is a good or bad idea:

    
    BOOL hasTopSelection = SomeFunctionToCheckForTopSelection();
    BOOL hasBottomSelection = SomeFunctionToCheckForBottomSelection();
    int numberOfSelections = hasTopSelection + hasBottomSelection;


I know you could write this:

    
    int numberOfSelections = 0;
    if (SomeFunctionToCheckForTopSelection()) numberOfSelections++;
    if (SomeFunctionToCheckForBottomSelection()) numberOfSelections++;


but the first example will avoid two conditionals. 

----

Generally     NO/false means zero and     YES/true means everything but zero.

For several methods in Cocoa this is emphasized.

In C++ though, you are guaranteed that if you cast a bool to int, it will take the form of zero or one.

With regard to your code, use inline if's:
    
BOOL hasTopSelection = SomeFunctionToCheckForTopSelection();
BOOL hasBottomSelection = SomeFunctionToCheckForBottomSelection();
int numberOfSelections = (hasTopSelection?1:0) + (hasBottomSelection?1:0);


Remember, functions often do not explicitly return YES, but instead e.g. return     str[i] == '\0' or similar (and the compiler knows nothing about     YES and     NO,     BOOL is defined to be a     char) type.

----

Yes, inline ifs work nice here. Your point about the return value being 8 bits wide is true (see TypeIDDoesNotCastToTypeBOOL). I guess I should never assume that a return of type BOOL will actually be the defined values of YES or NO (even when dealing with AppKit or Foundation objects).  

*I can only second that, though you can assume that the return value takes the form of     NO, but not     YES, as that is the complement of     NO, which basically means �all values not equal to     NO�.*

