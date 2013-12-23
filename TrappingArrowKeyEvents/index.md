---
layout: page
title: TrappingArrowKeyEvents
---



i am trying to obtain key events sent to my window and I have been successful. By subclassing General/NSWindow etc.

However, within the class I am trying to catch the up and and down arrow keys but it doesn't work. Can somone please look at the code below to see what's wrong? Thanks a lot.

This works
    
- (void)keyDown: (General/NSEvent *) event
{
	General/NSString *input = [event characters];
	if([input isEqual: @"\t"])
	{
		General/NSLog(@"Tab pressed");
		return;
	}
}


But this doesn't and I get a warning. I understand that General/NSUpArrowFunctionKey is an integer, but how do I actually compare it.
General/RealControl.m:27: warning: comparison between pointer and integer
    
- (void)keyDown: (General/NSEvent *) event
{
	if([event characters] == General/NSUpArrowFunctionKey)
	{
		General/NSLog(@"Up Arrow pressed");
		return;
	}
}

Neither does this work, and I get no warnings or errors
    �
- (void)keyDown: (General/NSEvent *) event
{
	if([event keyCode] == General/NSUpArrowFunctionKey)
	{
		General/NSLog(@"Up arrow pressed");
		return;
	}
}


Can someone please tell me what's wrong?
Thanks again.

Shaun.
 ----

Try General/ArrowKeyForKeyEquivalent, and then do a little digging, since that page deals only with left and right. The others should be close at hand.

----

Try     if (General/event characters] characterAtIndex:0] == [[NSUpArrowFunctionKey)

*I see this a lot, even an Apple guy posted this, but it is broken! It will throw an exception if the event contain no characters, and some key events do!*

----

This should work too.
    
- (void)keyDown:(General/NSEvent *)event
{
	General/NSString *input = [event characters];
	
	if([input isEqual:General/[NSString stringWithFormat:@"%C", General/NSUpArrowFunctionKey]])
	{
		General/NSLog(@"Up Arrow pressed");
		return;
	}
}

*What if two keys are pressed at once?*

Then you should figure out what logically makes sense in your situation. :-)
