---
layout: page
title: HighScores
---

I have succesfully intialized the plist.
I have successfully loaded the score onto the form with the following code.
The last line of this code where i'm trying to put the value of score1 into the score2 textField does not work,
I dont get the int value of score1, I get 0.
here is the code:

    
NSString *scoreName1 = @"scoreName1key";
NSString *score1 = @"score1key";
NSDictionary *dict = [NSDictionary dictionaryWithContentsOfFile:@"/Library/Preferences/sbhs"];
 if (!dict) NSLog(@"Failed to read the high scores.");
 [scoreName1TextField setStringValue: [dict objectForKey:scoreName1] ];
[score1TextField setStringValue: [dict objectForKey:score1] ];
    
// here is the problem
// I am only putting score1 into the score2textField for the purposes of testing.
// I need to see that it is an int so i can compare it to the current score.
// score1 is stored in the xml plist as a number.

[score2TextField setIntValue: [score1 intValue]];



----

it should be:
    
[score2TextField setIntValue: [score1TextField intValue]];

