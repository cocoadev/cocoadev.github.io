---
layout: page
title: ParsingUnspacedTerms
---

If I want to parse the string @"tORfNOTfANDNOTf" into an array containing each item of the unspaced string such that my array will look like:

0:@"t"

1:@"OR"

2:@"f"

3:@"NOT"

4:@"f"

5:@"AND"

6:@"NOT"

7:@"f"


What code would perform that parse faster than classic substring scanning?  Although it shouldn't matter to how the parse is performed, case is actually irrelevant.  I used lower case values and upper case operators but that shouldn't make a difference -- the parser should just as easily move through all caps.  If only I knew how to use lex and yacc... Any ideas?

----

Are you sure that what you're doing is a good idea?  After all, this forces you to make sure that all your operators don't have of your operators as substrings. How, for example, would you parse the string @"landorsea"? @"l" @"and" @"or" @"sea" or @"land" @"or" @"sea"?  Now that I've voiced my concerns, the simplest way to do what you want is to use one of the regular expression frameworks (such as OmniFoundation or MOKit) to match against your operators, and split the string along the match boundaries. -- Bo

Lex and/or yacc will not help in this problem.  Your "grammar" does not appear to be regular.
Some token to break things up would help immensely.  Using mixed cases does make it possible
to handle the expression above (logical operators are all uppercase and two can appear consecutively, e.g., AND NOT / OR NOT).  If you must use your current form, you will need to
look ahead when creating an operator because AND and OR have different number of characters
in them (not hard).  A state machine approach might be easiest. -- BruceB

