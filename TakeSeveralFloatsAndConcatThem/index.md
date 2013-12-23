---
layout: page
title: TakeSeveralFloatsAndConcatThem
---



OK. Here we go. All I need to do is take several floats in an array - let's call it myArray [6] - and concatenate them into a single string, separated by a delimiter. This will be sent out to a text field (called myTextField). I'd like to do something like
    
[myTextField setStringValue:(@"%d, %d, %d, %d, %d, %d",
    myArray [5], myArray [4], myArray [3],
    myArray [2], myArray [1], myArray [0])];

but I think I need some kind typecasting.

If someone would be so kind as to advice this poor n00b (me), I'd appreciate it!

- General/GeeFourOliver

----

OK! I got it with help from http://developer.apple.com/documentation/Cocoa/Conceptual/General/DataFormatting/Tasks/General/FormatStrings.html.

I went as such:
    
[myTextField setStringValue:
    General/[NSString stringWithFormat:@"%g, %g, %g, %g, %g, %g",
    myArray [5], myArray [4], myArray [3],
    myArray [2], myArray [1], myArray [0])]];


- General/GeeFourOliver
