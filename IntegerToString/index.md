---
layout: page
title: IntegerToString
---



I need to transform an integer into a string. At the moment, I have no clue how to do this and am using this code:
    - (void)refreshVals
{
    [totalDealer setStringValue: dealerPoints];
    [totalPlayer setStringValue: playerPoints];
}

Both dealerPoints and playerPoints and int constants.

As you might guess, I'm messing around with cocoa and trying to learn how to do things. The code snippet above is for a black jack (quite a sketchy one at that) game. 

----
(added this to CocoaDiscussions)

There are ofcourse multiple ways of doing this, and I don't really know which one's the best/most effective but you can sure use:
    
[NSString stringWithFormat:@"%i", dealerPoints];

where the %i part depends on what number you have. %i is an int (i think), %ld is a long, and %f and %g are floats.

In you code this is:
    - (void)refreshVals
{
    [totalDealer setStringValue: [NSString stringWithFormat:@"%i", dealerPoints]];
    [totalPlayer setStringValue: [NSString stringWithFormat:@"%i", playerPoints]];
}


But now one question: are totalDealer and totalPlayer NSTextField s? If they are, all this can be avoided, using one of these methods:
    
[totalDealer setIntValue:dealerPoints];
[totalDealer setFloatValue:dealerPoints];

----
Yes, well then. I think I tried setIntegerValue: but of course that didn't work. Thanks.
----
That's because it setIntValue: but you were close.
----

**Second verse, same as the first.**

I am trying to solve question 6 of chapter 6 in Stephen Kochan's book Programming In Objective-C. The questions asks you to write a program that will translate numbers into words. For instance if you type "932", the program's output should be "nine three two". I can't figure out how to accomplish this. The book's website only gives answers to odd-numbered questions, so this one isn't listed there. ----

Specifically, you need to consider that what you typed is already a string. A string is comprised of individual characters. How would YOU go about reading out the individual digits? You'd look at each character in sequence and say the word, right? Since program methods are only procedures for doing stuff, you need to express this process in code. That is, for every  'character' in the string, match that character to its word equivalent and output as needed. Things to check out would be NSString and for / while looping (under "flow control" in most books) in your favorite ObjectiveC (or even C book). 

----

I don't think I can use NSString because the book hasn't explored it yet so I'm assuming its possible without it. I don't think i'll even need arrays. I do have working code but the problem is it returns output in the reverse order, so for "932" it returns "two three nine". i am extracting the the right most digit by using the modulus operator (%). I just need to know how to get the left digits in order... or is there a different approach to this? I'll post code in a while, I've gotta run right now.

----

Well I really can't give you any more details as I have none from you. But be careful with printing material from a book - it is copyrighted material. An example probably falls under 'fair use', but I'm not sure of that. While using math operations in this way is certainly interesting, it's completely unnecessary and error-prone. If it's not an NSString, it's likely a CString. But it's still a 'string of characters' - treat it as such. It's also worth mentioning that if your code encounters a non-numeric character, it will break. Iterating through each character will solve your reverse problem and the potential for bad user input (looping through each character and testing if it matches "1" "2" or "3", etc. will do nothing if it doesn't match, while your math approach assumes it's all numbers - bad move, crash city).

----

I suggest a trip to Google. Search for 'convert numbers into words algorithm'. There are algorithms posted. You'll never learn anything if you ask someone else to give you the answers that aren't provided for you.

----

It is true that requests for help with homework tend to get wayward answers: it is not really a copyright problem, though, more than the fact that the people with the knowledge and experience to post correct answers identify more with the author than with you! (A small excerpt from a book, especially for personal study or for scholarly analysis, is fair use; but in some countries a 'standalone' work, such as an example, may have copyright encumbrance in its own right - but the fact is that one is not playing fair by publishing a solution when the original author purposefully did not).

For a working example of something similar, could I refer you to the Elephants and Tigers, videlicet http://wdb.apple.com/downloads/macosx/math_science/romanumerator.html, now this project is Java rather than ObjectiveC, but you could, as a warm up, translate it into ObjectiveC having the the working model beside you. More interestingly, do you think that the interface of this project could be improved: Is the pop-up widget necessary? Should one be able to tab out of fields? Could one make use of a time?

FWIW, the first Hypercard XCMD I wrote took a number as input like 007 and read it out - spoke it - as "Aught, Aught, Seven".

----

Hint: Without string parsing, you need *no more than* the  %, /, *, and - operators, and a constant, say, 10. I guess that just means I favor arithmetic over text analysis when either will do. And integer arithmetic will suffice, assuming you store the input "932" in an     int variable, which I take to be the case, since you've already tried the modulo operator on it. 

Maybe you can do it with fewer. There's probably a proof somewhere, but I am not a CS student so I could not tell you where to look for it. K&R is full of exercises like this. It's also bollocks to say that publishing a solution is unfair to anyone but the student.

**Remember, the class references are your friends.**

Kochan's book is frustrating at times. It does jump a bit, seeming to assume no prior experience, but then assuming some things are understood already. I'm struggling to understand Chapter 7 Exercise 2.
But I can help with Chapter 6 Exercise 6.
He says it is a hard one, and it is. You won't get a bullet proof program with what the book presumes you have learnt to that point, but you can make it work. You can do it with only what you have read and learned to this point in the book. You should take the original program to reverse the digits, and use that while or do while block a second time.
Once to reverse the digits as before, second to reverse them again and you will need something like a switch case block for the words. The exercise is a good one. It's about the algorithm and puzzle more than about best practice in programming.
(last hint: pay close attention to the operators used)

