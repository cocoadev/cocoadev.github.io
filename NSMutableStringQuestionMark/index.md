---
layout: page
title: NSMutableStringQuestionMark
---

Describe NSMutableStringQuestionMark here.


Greetings.

This is my first post to this forum, and I admit that to me, this seems like a strange way to run a railroad. But I have high hopes.

I'm slugging my way through a new book, "Learn Cocoa on the Mac", by Jack Nutting, David Mark and Jeff LaMarche.

The book teaches by giving complete examples, and then sparse explanations of the code used. The book is extremely dense, and packs a lot of information into a minimum of words, and one must parse paragraphs and track down sentences carefully to learn the information.

Therefore, I copy the examples, and dissect the code piece by piece until I understand how it works; up until now, this method has served me well, I've learned a lot.

If any of you have this book, or if any of you are reading it as I am, I will include references so you can see what I'm seeing, but I'll try to keep the book out of it, and focus on the issues that are confusing me.

Starting in chapter 6, on pager 105, one starts building an app called "DungeonThing", which is a tool that would help a dungeon master in a game of Dungeons & Dragons. The intent of the chapter is to teach Cocoa Bindings.

The code that's confusing me looks like this:

/************/

#define kMonsterTypeAllowedOrc @"monsterTypeAllowedOrc"

/************/
- (IBAction)createMonster:(id)sender
{
	NSUserDefaults *ud = [NSUserDefaults standardUserDefaults];
	NSMutableString *result = [NSMutableString stringWithCapacity:1024];
	[result appendString:@"Generating a monster within these parameters:\n-------------------------\n"];
	
	[result appendFormat: @"Booty Frequency: %d\n",
	 [ud integerForKey:kMonsterBootyFrequency]];
	
	[result appendFormat:@"Allows Orc: %@\n",
	 [ud boolForKey:kMonsterTypeAllowedOrc] ? @"YES" : @"NO"];

/************/

It's that last section that gets me. I understand how a copy of the User Defaults is created, how a string is created and shoved into result, and how the rest of the display is then appended to the end of it, building what is displayed section by section.

What is baffling me is the question mark and the "YES" and the "NO".

Searching through the documentation, the message
	[result appendFormat:@"Allows Orc: %@\n",
	 [ud boolForKey:kMonsterTypeAllowedOrc] ? @"YES" : @"NO"];

is a call to appendFormat, which is an instance method of NSMutableString. I found that in the Apple documentation. 

The brackets of the message include the entire statement, the results are going into result, then the call to appendFormat:, then the first parameter, an NSString that consists of "Allows Orc:", and a format that specifies that the following parameter is an object.

Now we come to my problem.

The next parameter, which follows the comma after the format, is a set of brackets that first calls boolForKey: and asks for the boolean value in the user defaults at the key kMonsterTypeAllowedOrc.

The way I read that, that should complete the call. The boolean in the array stored at the key kMonsterTypeAllowedOrc should be jerked out, appended to the string, and game over.

But no. There is then the mysterious question mark, which I can't find in any documentation, not the documentation for Objective C, not the documentation for NSMutableString or the call to appendFormat, or in any of the documentation for how to format strings.

What follows the question mark is a "YES" and a "NO", both declared as NSStrings by the shorthand method, that I found in the documentation to NSSTring, separated by a colon which would normally indicate another section of a selector.

Except that the call is already complete, the question mark doesn't indicate anything that I can find, and there isn't any selector calling anything beyond the closing bracket!

I've messed around, changing stuff, eliminating the mysterious question mark, and unless it's written just this way, it won't compile.

So what I'd like is this: I'd like to find the documentation that I'm obviously missing, I'd like to find documentation on the question mark, and how the "YES" string and the "NO" string at the end of the call are attached to the call.

There is documentation that says there can be lots of parameters at the end of a call to appendFormat, they are comma delimited.  

But I can't find any documentation about the question mark, not in NSString, not in appendFormat, not in the documentation about how to format strings, not in Objective C. Don't get me started on printf.

Why does it require the two NSStrings "YES" and "NO" at the end, when the dictionary entry at kMonsterTypeAllowedOrc contains a perfectly acceptable boolean value that will fit very nicely onto the end of the statement we're trying to build?

The reason I'd like this documentation is because I'd like to know under what circumstances such strings are necessary, so I can build them in the future if I need to.

But above all, I'd like to find documentation on that mysterious question mark, and when to use it.

So I turn to you, and ask for your help.

Thank you.



----

You need to look up the ternary operator: http://en.wikipedia.org/wiki/Ternary_operation



----

Thank you. Very much.

Studying your answer, I'm not certain I see the point, unless it's to have exact control over the result.

It still strikes me that there's a perfectly good boolean in the dictionary, that would serve quite well as the result, and the ternary (pronounced, I take it, [likeTheBird & airy] operator just forces the code to choose between two phrases that are, in this case, identical to what's in the variable.

The other, more significant result is that I took "ternary" and "ternary operator" to Apple's Objective C reference, and after a cursory search (haven't had time to sit down, break out the fine tooth comb and search properly) the only references I found were to GDB debugging. I intend to do a real search this afternoon, now that I have the name of the operator.

----

Step one: the "Back In The Day" step. Looking up "ternary operators" in Kernighan & Ritchie, in the index, nothing. However, I note that some genius has highlighted a section in the index on "operators", on the opposing page. Bears investigation.

The second entry takes me to Appendex A, the Reference Manual, and there, in A7.16, is the question mark. However, it is merely there, with no explanation. The Wikipedia explanation is far more helpful. But yes, I've been responsible for knowing this operator for a long time... [sigh long].

----

You're right that there's a boolean in the dictionary, but if the point is do display something user-readable, you typically don't want to show just a 0 or 1! If you wanted to do that, you could shorten the code to this (note the replacement of %@ with %d):


	[result appendFormat:@"Allows Orc: %d\n",
	 [ud boolForKey:kMonsterTypeAllowedOrc]];


But then you'd end up appending "Allows Orc: 0" or "Allows Orc: 1". The "YES" or "NO" business is just a way to make the output slightly more user-friendly.

