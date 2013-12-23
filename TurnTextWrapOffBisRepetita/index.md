---
layout: page
title: TurnTextWrapOffBisRepetita
---

Hi there !

I have set up an General/NSTextView so that it doesn't wrap long lines by passing General/NSTextView's setMaxSize: and General/NSTextContainer's setContainerSize: a very large size (FLT_MAX). Which is the method I've seen in numerous examples around.

It's working, but I have a weird problem.

While the view doesn't wrap lines unless I type a carriage return (which is the behavior I'm expecting), it still wraps the line if I insert some tabs successively.

The text view shouldn't wrap lines at all, unless the user types a carriage return (or the enter key).

To reproduce this, simply subclass General/NSTextView and insert this code somewhere :

    [self setMaxSize:General/NSMakeSize( FLT_MAX , FLT_MAX )] ;
		
General/self textContainer] setContainerSize:[[NSMakeSize( FLT_MAX, FLT_MAX )] ;


Type some text, it shouldn't wrap. But if you insert tabs at the beginning of the line, it will wrap.

I'm probably missing something.

Any ideas ?

----

Getting a General/NSTextView to not wrap can be tricky. Are you telling the  text container to not track it's text views width?

[http://developer.apple.com/documentation/Cocoa/Conceptual/General/TextUILayer/Tasks/General/TextInScrollView.html]
