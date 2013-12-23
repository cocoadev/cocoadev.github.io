---
layout: page
title: PollCodingStyles
---

I'm just curious how people (that care about coding styles) actually code:

APPL - Apple's documentation, K+R - (Kernighan & Ritchie), ABY - Anguish/Buck/Yacktman, RS - Richard Stevens, ST - Smalltalk, AH - Aaron Hillegass

*I don't have a copy  of Aaron Hillegass's latest and greatest, so if someone with time and a copy of his book is interested in filling in how AH codes that would be nice*

**spacing** assuming     i has not been initialized
    
(i=0;i<10;i++)         [1]
(i=0; i<10; i++)       [5]    ABY*
(i = 0; i < 10; i++)   [14]    K+R, ABY*, RS, AH
( i = 0; i < 10; i++ ) [1]


**' since this book was written by three authors, I'm not surprised that the coding style was not totally uniform (overall the general style was pretty consistent)*

**if brackets** spacing issues between     if and "(" is addresssed in the *for/if/while spacing* section, this section is only concerned with bracket placement
    
if (i < 10) {                [12]    K+R, ABY, RS, AH

}            

if (i < 10)                  [8]
{
}

if (i < 10)                  [1]
    {
    }


**else brackets**
    
}                              [1.25]
else {

} else {                       [9.75]    K+R, ABY, RS

}
else                           [9]
{          

    }
else                           [1]
    {


**for/if/while spacing**
    
while(test)                  [8]    ABY*
while (test)                [12]    K+R, ABY*, RS, AH
while ( test )              [1]


**' here again the guys who wrote Cocoa Programming have different styles*

**method brackets**
    
- (void)someMethod {          [5]
}

- (void)someMethod            [15]    K+R, ABY, RS, AH
{
}

- (void)someMethod            [1]
    {
    }



**method spacing**
    
- (void)someMethod:(id)object        [16]    ABY, APPL* 
- (void)someMethod:(id) object       [4]
- (void) someMethod:(id)object       [1]


**' most of Apple's headers use this method spacing*

**message spacing**

    
[target fooWithObject: bar]             [4]     ST*
[target fooWithObject:bar]              [12]     ABY**, APPL, AH

* Note the space after the colon.  The syntax and semantics for message expressions in ObjC were modeled after the syntax and semantics for message expressions in Smalltalk, where a space after the colon is widely regarded as a best practice.  See, for example, Kent Beck, Smalltalk Best Practice Patterns; Skublics, Klimas and Thomas, Smalltalk with Style; Simon Lewis, The Art and Science of Smalltalk; and Chamond Lui, Smalltalk, Objects and Design.

** This style was was adopted in two terrific Cocoa textbooks---Anguish, Buck and Yacktman, *Cocoa Programming* and Aaron Hillegass, *Cocoa Programming for Mac OS X* ---as well as recent Apple Developer Documentation on Cocoa conventions.

**function spacing**
    
srand( time )                                    [2]
srand(time)                                      [17]    K+R, ABY, RS
srand (time)                                     [2]


[Topic]

----

Open brackets on a line by itself is called K+R style, and the other way is GNU style, I think... I can never remember which is which. *Bzzt wrong way round*

----
Something I think not mentioned here is that you can use different styles as a kind of documentation, indicating whether a function is on the periphery of your code, for instance, or called routinely.  This can be useful later when determining where you may want to focus optimization: profiling helps, but it's nice if you have some notes to yourself, and it also helps if you want to change the implementation, you know whether or not a bug will show up right away or not.  It is by no means a subsitute or stand alone practice -- just one more aid on top of many others.
*I think this a horrible idea, to put it bluntly.  That's what well-written comments are for.  When you get hit by a bus (or find a new job), the poor schlub who has to finish/maintain your code is going to have no idea why you use different styles in different places.  If you use your effort to put comments (like  // this is untested ), then everybody will be able to figure it out.*

----

Also note that K+R style is much more compact than GNU. Many times books will use K+R just to save pages, regardless of how the authors *really* code.

----

To keep this poll clean I condensed DimitriT's answers into the total results above. Hope this is ok.

