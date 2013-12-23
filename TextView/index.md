---
layout: page
title: TextView
---

I have posted this question a while before, still looking for a solution.

I need to display a report in the General/TextView field. Something like this:


     
VGVCVC		33	2	1	33	100	100
VCC		23	3	0	1	0	2
VCVGCCCC	3	1	0	3	90	100
VC		3	5	6	2	34	1


     
But I get this instead:

VGVCVC	      33      2        1	33	100	100
VCC	23	3	0	1	0	2
VCVGCCCC       3	1	 0	  3	  90	  100
VCCC	2`	7	100	3	6	1
VC	3	5	6	2	34	1


I am using - General/NSString initWithFormatedString:  and using tab spaced in the formatedstring. Then get attributedString , then display textView with the attributedString .


I noticed that problem happens for any pattern which has more than 4 letters( e.g. VGVCVC, VCVGCCCC in above example), it pushes the tab space backwords. I am guessing it is related to  OSX UTF8String in displaying? 


It's difficult for me to use tableView in this case. I would not know rows and colums ahead of time -- Rows are obtained dynamicly, and I get each row from a for loop.


Any trick to align them up?


Phyllis

----

Tab just aligns to the next multiple-of-8 column, even at the best of times. Try using a General/NSTableView instead. You know you want to...

You could always store the results in an General/NSArray or something, so you only need to work them out dynamically once. Then the table view may be possible.

Is it actually the number of **columns** that you don't know before hand?

-- General/KritTer

I have an General/NSSegmentedControl in the toolbar of my app which, when pressed, changes the alignment of the General/TextView. I need to know when a user changes the alignment using the menu instead of the General/SegmentedControl so that I can change the highlighted cell to reflect the current text alignment. I also need to know when the alignment has changed within a document (ie: on line 4, it is aligned to the right, but on line 5, it is centered. When the user moves the cursor to line 5, I need to change the General/SegmentedControl to reflect this). Is there some sort of General/NSNotification that will help me with this? Any help would be appreciated.

-- General/MattBall

----

You can use     General/NSTextViewDidChangeTypingAttributesNotification or     - (void)textViewDidChangeTypingAttributes:(General/NSNotification *)aNotification in your delegate and get the typingAttributes from there, which contains a     General/NSParagraphStyleAttributeName, which is a General/NSParagraphStyle object which you can retrieve the alignment from.
