---
layout: page
title: StringFractionToFloat
---



I am in need of converting a fraction contained in an General/NSString to a float, I could write up something with General/NSScanner myself however due to the nature of General/NSScanner and it's speed i'd rather not.

What would you all think would be the best way to do this?

*How about General/NSString's     floatValue or     doubleValue? I don't know how efficient it is, but assuming you could isolate the right part of the string, this should work fine. For finding the right part, just search for whatever is directly before the float. --General/JediKnil*

Well I use that right now for other things, but the problem is a fraction ie. "1 1/2" that I need to convert to a float. Which I could find the parts of using General/NSScanner, but i'm wondering if there is a better way (without writing a compiler).

----

Perhaps you could use Regex to parse out the fractions, evaluate them, and then do whatever?  -- That should be faster than General/NSScanner I would think.

*Using a regexp would at least make the code easier to understand, usually code that uses a scanner becomes a bit tricky to follow, lots of ifs and long lines, and things. Regexps encapsulate that in a simpler statement. --General/TheoHultberg/Iconara*

**Haha - there's something perverse about recommending regexps for enhanced readability.  I completely agree, but I find it amusing.** 

----

Take a look at http://homepage.mac.com/tritchey/.Public/General/RRFractionFormatter.dmg to see how someone else did it.  There's a subclass of General/NSNumber with a     +fractionNumberWithString: method. 

----

Well that code didn't work, but I did glance at it to see how he did it, here is the code I wrote:

    			
// Create variables.
General/NSString * tmpString = nil;
int * fullPart = NULL;
int * numerator = NULL;
int * denominator = NULL;

// Create scanner.
General/NSScanner * scanner = General/[NSScanner scannerWithString:
	[amount stringByRemovingSurroundingWhitespace]];

// We need to figure out if this fraction has a number in front of it.
[scanner scanUpToString:@"/" intoString:&tmpString];
BOOL hasFullPart = General/tmpString stringByRemovingSurroundingWhitespace] containsString:@" "];
[scanner setScanLocation:0];

if (hasFullPart)
{
	fullPart = malloc(sizeof(int));
	[scanner scanInt:fullPart];
	[scanner scanString:@" " intoString:&tmpString];
	//[[NSLog(@"fullPart:%i",*fullPart);
}

// Get the numerator
numerator = malloc(sizeof(int));
[scanner scanInt:numerator];
[scanner scanString:@"/" intoString:&tmpString];
//General/NSLog(@"numerator:%i",*numerator);

// Get the denominator
denominator = malloc(sizeof(int));
[scanner scanInt:denominator];
//General/NSLog(@"denominator:%i",*denominator);

float floatRep;
if (fullPart == NULL) {
	floatRep = (float)*numerator/(float)*denominator;
} else {
	floatRep = (float)*fullPart + (float)*numerator/(float)*denominator;
}

//General/NSLog(@"floatRep:%f",floatRep);

free(fullPart);
free(numerator);
free(denominator);
 




----

*And now to a theoretical discussion about speed. If you're not parsing a string a quintillion times each second, you may just as well ignore these comments, and use the time it would have taken to read them to write maintainable code instead. =)*

----

Perhaps you could use Regex to parse out the fractions, evaluate them, and then do whatever?  -- That should be faster than General/NSScanner I would think.

----

In theory at least a regular expression is slower than a scanner. The slow thing about General/NSScanner is having to deal with General/NSString (e.g.     valueForKeyPath: is also significantly slower than multiple     valueForKey:'s, since the former needs to �parse� the string).

If you're converting zillion of strings to fractions every second, and thus actually need the improved performance you speak of, you should ditch General/NSString!

----

Dangerously skirting the edges of off-topicism here, but regexes can be just as fast as a "dumb" scanner in theory, and probably in practice as well, at least if you're constantly using the same regex. Any half-decent regex engine will compile your regex into an extremely efficient state machine, where the string is scanned once, and each character looked up in a table to see what to do next.

*There are two kind of state machines, the one you speak of is a DFA, which is indeed linear in input (with potential exponential storage requirement, in the pattern length) -- but almost any regexp library out there uses an NFA, which is potentially exponential in running time. The reason being that a DFA can't support captures, counted repeats, and a hell of a lot of the other features which exist in most regexp libraries.*

You should have paid more attention to whatever class you learned those terms in. In fact, you can algorithmically translate any NFA to a DFA, and vice versa. This means that they are, of course, equally powerful. The stuff you mention can't be handled by either, and you're right that they'll be slower. However, using only *traditional* regexes will give you an extremely fast scanner.

*Oh� so a regexp library that offers captures (which basically all of them do) can't be implemented using a state machine? and yet (assuming you're the same) you said above that the OP should use a regexp library for performance, and that the performance came from using a DFA�*

*And please spare me your condescending remarks, I have implemented both DFA-based matchers and an NFA-based regular expression library. While true that an NFA can be transformed into a DFA, the features the regular expression libraries add to the equation generally comes from the way they _use_ the NFA (the General/ECMAScript specification even mentions that the regular expression is done using a back-tracking NFA) -- normally of course, the regexp library will use some �extended� NFA to keep extra node info, but most still refer to it as an NFA (since the structure/graph is the same), though there are of course also regexp libraries which construct sort of a byte code program, but the time complexity is still that of the NFA (since they need state info, which the DFA can't give).*

*Lexical analyzers (like lex/flex) are probably the only useful matchers which are DFA-based.*

As far as I can tell, not doing a great deal of research on it, GNU grep is based on a DFA and it is *extremely* useful.

----

Is there a point to continuing the above argument?  Anyway, to the italicized poster - thanks, I learned something!  I had heard of this DFA - NFA distinction before, and was confused because I knew every NFA could be transformed into a DFA.  Now I understand why people distinguish between the two. 

----

Is a regex library still going to be slow because it supports captures and repeats, even if you never use them?

*Yes and no :) even if you don't use the features which require the regexp library to use an NFA (inspired) state machine, it generally will use it anyway -- there are a few exceptions, for example many will catch literal sequences of text and switch to a sub-linear search algorithm for that sequence.*

*However, if you do not use repeats (which are btw possible with a DFA, but counted repeats aren't, like     (foo){3,7}, without expanding of course (which for small repeat counts may be a reasonable workaround)) then the running time of the NFA approach will be near linear -- alternations like     (foo|bar|fud) will still cause backtracks, but that's insignificant, the real (performance) problems arise from nested wildcarded repeats, or (with the newer breed of regexp libraries) using recursion.*

*As for GNU grep, the initial grep was enhanced into egrep by Alfred V. Aho (iirc) which used lazy construction of General/DFAs (problem with DFA construction is that this may be exponential in time, and worse, use exponential storage -- so Aho's hybrid was good). Of course egrep then only supports a true regular language (like BNF). I'm not sure about the latest GNU grep, since it e.g. supports back-references, which would imply captures (which as mentioned, isn't possible with a DFA), but looking at the grep man page, it indeed does end with this (under bugs):*

**Backreferences are very slow, and may require exponential time.**

It's clear you know lots more about this than I do; sorry about the "class" crack above. In any case, my point still manages to survive: a good regex with a good regex engine should be quite fast.... and then you can use it to convert your General/StringFractionToFloat. :-P
