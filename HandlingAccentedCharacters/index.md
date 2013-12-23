---
layout: page
title: HandlingAccentedCharacters
---

My app needs to store individual letters for which I'm using NSStrings. This works great for single character letters but doesn't handle accented characters correctly. Is there an easy way to treat accented characters as single entities? I looked at NSString's -precomposedStringWithCanonicalMapping which appears to convert the decomposed form to the composed form, but it wasn't that simple (is it ever ;-).

Basically I have an -insertText: method in my class which receives the typed input from the user via the usual route (keyDown, which calls interpretKeyEvents). Accented characters arrive as separate events... so I guess my question is how do I correctly coalesce these events into a single precomposed string for accented characters? (GrahamCox)

here's what I tried first - this is part of my -keyDown: method where <evt> is the event parameter I'm passed. (It doesn't help, though it's a slight improvement in that it at least ignores the dead key event):

    

		static NSMutableArray* eventQ = nil;
		
		if ( eventQ == nil )
			eventQ = [[NSMutableArray alloc] init];
			
		// queue the event:
		
		[eventQ addObject:evt];

		NSString* chars = [evt characters];
		
		// if the string is empty, just ignore it this time
		
		if (! [chars isEqualToString:@""])
		{
			// a normal character, so process the whole queue
		
			[self interpretKeyEvents:eventQ];
			
			// empty the queue ready for the next one
			
			[eventQ removeAllObjects];
		}



OK, more digging... seems this falls into the category of a custom view that isn't a text view but which needs to support the NSTextInput protocol. According to the Cocoa docs this seems to be classified as a "very advanced topic". That's pretty sucky - surely it shouldn't be this hard :( This protocol seems to assume that the text is stored in a block and that the view will be doing some layout on it. Neither of these things is true in my case (it's a crossword puzzle grid for the curious - naturally it stores "text" as individual letters in cells). Whether I can bend the protocol to my will without a major redesign I don't know... if I come up with anything I'll post it here. Depressed.

----
If you use     rangeOfComposedCharacterSequenceAtIndex:, you can find the grouping of characters that behave as a "single entity", maybe that would be enough for your needs.

----

The problem is I don't have a composed string to begin with. Each typing event arrives separately, so e.g. option-e, e arrive as two events. -    -interpretKeyEvents: doesn't coalesce them as I at first assumed was its job (all it seems to do is to separate keyboard command events from typing events), so I'm still dealing with two events (or by this stage, strings) at my     -insertText: method. However at this point, things appear to be even worse, since the option-e event simply arrives as an empty string, with all other information stripped from it. That means that I don't have the opportunity to make a composed string at this stage by concatenating this with the following character string. Clearly, the composing must be done at the event handling stage where there is still information available about what the user actually typed. Cocoa's solution is a very complicated protocol between the input manager and the responder that first "marks" the dead key as such and then when the next characters arrive it can compose the text. At least I think that's how it works - I'm still trying to get my head around it. As far as I can see this is the only reliable way to implement this with all possible input methods. It's complicated, but sadly no shortcuts seem to be avaliable. If anyone has expereince of the NSTextInput protocol and can help me out here I'd be very grateful!

----
Ah, I see the problem now. Maybe you can just grab the window's field editor and let it do all of the hard stuff?

----
As the above poster says, use the window's field editor.  We had a custom view that needed text input from the user, and what we ended up doing was using a 100% transparent window's field editor to accept the text input, and using the NSText(View)'s delegate methods to send the resulting text over to our view.  It worked great.  See -[NSWindow fieldEditor:forObject:] for a starting point. -AndrePang

----

Cunning! :) Sounds like a good way forward - I'll let you know how it works out. Many thanks!

