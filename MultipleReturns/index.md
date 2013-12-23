---
layout: page
title: MultipleReturns
---



Dicsuss the wisdom of having multiple returns in a function or method.  (Taken from PollCodingStyles)

    
if (flag)                                            [2]
	return YES;
return NO;

if (flag)                                            [1]
	return YES;
else
        return NO;


*return flag* is missing the point here; imagine that besides simply returning the value of the condition, you have to do something else that is condition specific **before** returning. 

And does this have any effect on whether the branching unit is called into action, or does gcc optimize that anyway?

(note that this is questionable since multiple explicit returns are generally frowned upon since they very frequently lead to errors in a future iteration of code maintenance) **-- that's (kind of) news to me.  Can you elaborate a bit?**

When expanding a function to call some other functions (as an example of increasing complexity of a maturing product), making sure that the control flow still makes sense around the new code becomes highly error prone when the function has multiple exit points.  That, and it is difficult to add meaningful debugging code to a function that tends to have exit conditions strewn throughout it.

*and it is difficult to add meaningful debugging code to a function that tends to have exit conditions strewn throughout it* - do you think goto statements are ok then?

With leaf functions it isn't as big a deal but they don't always remain leaf functions.

----
One problem that arises frequently, is multiple exits often require duplicate cleanup procedures (e.g. release allocated memory)  -- even if macro'd it creates a larger code footprint.

----

That issue of cleanup is one which led me to a strict policy of only one exit point.  Actually, it was that and debugging code (it is really annoying when you are trying to debug a complicated function and you are banging your head against a wall because your debugging code never gets executed and then you find some return statement in a code-path which you originally thought shouldn't have something come after it).

I tend to be afraid of duplication not just because of increased code-size, but also because you know that you can't keep propagating changes through every duplicate operation (not to mention how each of these cases begins to diverge after a while).

As for goto statements, I don't like them since they remove the notion of a code path which is something that C has over ASM.  Goto statements (in the use which I think you are implying) seem to be a patch on this multiple exit problem but they only address some of the problems.  I tend to use the control flow structures in C to avoid the problem entirely.

--JeffDisher

----
Goto Considered Harmful, settled the goto question once and for all... and code duplication is to be avoided at all costs for sure.  Are there any other reasons multiple exit points in a function or method should be considered bad design strategy?

----

Isn't it in Pascal that you state which variable will hold the return value at the start of the function? Nice touch anyway, that way it's clear where the return value is. You can still write confusing code though... just a thought late in the evening.

The GRASP-pattern (oops, redundant acronym syndrome) HighCohesion states that a method should do as litte as possible (meaning, doing only one thing, one side-effect or one calculation). This means that multiple returns are not much of a problem, if your method is highly cohesive, there will most likely be little confusion over what is happening.

--TheoHultberg/Iconara 


----

Taken from Darwin source...

    
static Boolean __CFStringEqual(CFTypeRef cf1, CFTypeRef cf2) {
    CFStringRef str1 = cf1;
    CFStringRef str2 = cf2;
    const uint8_t *contents1;
    const uint8_t *contents2;
    CFIndex len1;

    /* !!! We do not need IsString assertions, as the CFBase runtime assures this */
    /* !!! We do not need == test, as the CFBase runtime assures this */

    contents1 = __CFStrContents(str1);
    contents2 = __CFStrContents(str2);
    len1 = __CFStrLength2(str1, contents1);

    if (len1 != __CFStrLength2(str2, contents2)) return false;

    contents1 += __CFStrSkipAnyLengthByte(str1);
    contents2 += __CFStrSkipAnyLengthByte(str2);

    if (__CFStrIsEightBit(str1) && __CFStrIsEightBit(str2)) {
        return memcmp((const char *)contents1, (const char *)contents2, len1) ? false : true;
    } else if (__CFStrIsEightBit(str1)) {	/* One string has Unicode contents */
        CFStringInlineBuffer buf;
	CFIndex buf_idx = 0;

        CFStringInitInlineBuffer(str1, &buf, CFRangeMake(0, len1));
	for (buf_idx = 0; buf_idx < len1; buf_idx++) {
	    if (__CFStringGetCharacterFromInlineBufferQuick(&buf, buf_idx) != ((UniChar *)contents2)[buf_idx]) return false;
  	}
    } else if (__CFStrIsEightBit(str2)) {	/* One string has Unicode contents */
        CFStringInlineBuffer buf;
	CFIndex buf_idx = 0;

        CFStringInitInlineBuffer(str2, &buf, CFRangeMake(0, len1));
        for (buf_idx = 0; buf_idx < len1; buf_idx++) {
            if (__CFStringGetCharacterFromInlineBufferQuick(&buf, buf_idx) != ((UniChar *)contents1)[buf_idx]) return false;
        }
    } else {					/* Both strings have Unicode contents */
	CFIndex idx;
        for (idx = 0; idx < len1; idx++) {
            if (((UniChar *)contents1)[idx] != ((UniChar *)contents2)[idx]) return false;
        }
    }
    return true;
}


----

Note that each return statement increases the CCN (CyclomaticComplexityNumber) of a method. In general, the higher the CCN of a method, the more likely it is for that method to have a bug. And the more difficult it is to modify that method if necessary. 

I certainly don't mind returns in guard clauses at the top of a method. I strive to allow only one return statement after the guard clauses. I dont' always succeed.

TimHart

