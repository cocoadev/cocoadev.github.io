---
layout: page
title: NSRulerAndMarkersMADNESS
---



I've been playing around with the example code of TextEdit....

...and I'm dying....

Basically, I just want to modify the starting locations of the tabs in the NSRuler of the NSTextView and I'm having a hell of a time trying to access the Markers array of the NSRuler.

Here's what I've got so far...

    
//This is in the Document setup
// get the NSTextView
NSTextView *textView = [self firstTextView];

//change the font
NSFont *theFont = [NSFont fontWithName:@"Courier" size:12.0];
[textView setFont:theFont];

// get the NSScrollView of the NSTextView to get to the NSRuler
// why can't I get the NSRuler from the NSTextView??????
NSScrollView *sv = [textView superview];

// get the NSRuler from the NSScrollView 
NSRulerView *rv = [NSScrollView rulerViewClass];

// now when I look in the debugger, rv seems to be a proper NSRulerView

// try to get the array of markers
// THIS IS WHAT DOESN'T WORK
NSArray *ma = [rv markers];


I get this in the run log "+[NSRulerView markers]: selector not recognized"
The documentation for NSRulerView says that there's a markers array, but I can't seem to get to it....

How do I access the markers array of my NSRuler?

HELP!

Thanks!
	
----

You're not getting an NSRulerView instance. Your first clue to the error is the "+" in front of [NSRulerView markers]. This shows you it's trying to send -markers to the NSRulerView CLASS, not an instance.

Looking earlier in your code you're asking the scrollview for the *class*, not the instance itself. Check out the NSScrollView documentation and you'll see that -rulerViewClass hands back a *class*. You want -horizontalRulerView and -verticalRulerView.

