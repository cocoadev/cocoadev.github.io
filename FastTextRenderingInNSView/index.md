---
layout: page
title: FastTextRenderingInNSView
---



I've recently implemented a threadsafe variable tracing API for my AI runtime; the back-end is done, but I'm ready now to build a window to display the traced variables. The window would be simple, just a bunch of strings showing the name, type and value of the traced variable -- probably in a grid. 

These values are likely to change fairly often -- in some cases as much as 40 times per second (maybe more), though in practice, not all will change that often and some will almost never change.

I'd use an NSTable but in my experience it's not very good for continuously changing information.

Anyway, what I'm wondering is if there's a more efficient way to draw an arbitrary string -- likely a short one, just a number -- than by using something like:

    

NSPoint loc = NSMakePoint( ... );
NSString *trace = [NSString stringWithFormat: @"%f", someValue ];
[trace drawAtPoint: loc withAttributes: nil];



Admittedly, this is short, legible, and sweet. So from a code standpoint it's perfect. But I imagine there are ways using Quartz to directly draw a string at a point in an NSView.

Any ideas? Or is this not even worth examining?

Another option is to simply have a timestamp for the last time a value was written and not redraw the view unless, say, 200 millis has passed. This would effectively cap my redraw to 5 times per second which is reasonable simply because my eyes wouldn't be able to make sense of 40 times per second.

Also, it's important that this it as efficient as possible from a CPU-time standpoint since I want to devote as many cycles as possible to the AI -- not the Gui.

--ShamylZakariya

If you don't need any fanciness, or even if you do possibly, maybe you could use a read only NSTextView?  No gaurantees this will be faster however.

- FranciscoTolmasky

----

I believe (from FastWritesToNSTextView) that he's already tried and discarded this.

You could try drawing the text into an OpenGL view - that will happen almost entirely in your graphics card and leave the CPU alone. GLTerm [http://www.pollet.net/GLterm/] does this, but I have no idea if the performance improvement still holds with Quartz Extreme

----

Beware of PrematureOptimization. -- JosephSpiros

----

Yes, but also beware of writing sloppy, inefficient code that you will optimize later; but which in reality you will forget about because it works "well enough" -- until 2 months later when you notice it's a huge bottleneck thanks to Shark and then you slap your forehead. 

Of course you shouldn't perform low-level optimizations too early but I wouldn't consider the above question low level. I made damn certain that the actual number of redraws is as minimal as possible, and I've capped the update to 5 times per second. But I'm serious that my AI needs as many cycles as possible and between physics, visualization ( OpenGL ) and various Cocoa inspector systems there's not much left in my wee 1st gen 12" powerbook.

As it stands, with the above in consideration my display code's fast enough that I don't need to worry about a faster string drawing algorithm; but I might at some point, and I'd like to know -- and others may as well.

--ShamylZakariya

----

I personally wouldn't go for OpenGL for textdrawing, but then again, I have no experience whatsoever with OpenGL either. I recently managed to get huge speed increases in my app by leaving the drawAtPoint convenience methods, and use NSLayoutManager directly. As the name suggests, the NSString convenience method is really convenient, but terribly slow. My DNA sequence viewer went from 11s to scroll to the first 200 dna bases to just over 2s... A 500% speed increase, not too bad!
Have a look at the CircleView demo available from Apple. The reason why this is so much faster is that you save the NSLayoutManager and NSTextStorage objects, and their states. In contrast, the convenience methods have to create all these every time you draw an NSString. It's quite a big thing to get in the beginning, but the sample code available should help you get started.
Good luck!

--Alex (Mekentosj)

Now, this is VERY appealing. This is exactly what I was looking for, but didn't know how to ask for ;) I've read the sample code and some docs on apple's developer site and it looks quite promising. I'm going to give it a shot, presently. Just have to finish up at the day job, first...

--ShamylZakariya

----

Funny, or not so funny, I implemented the new approach and with benchmarking it's about 20% *slower* than the vanilla drawAtPoint implementation.

My guess is that this is happening because drawAtPoint doesn't expect to do any layout but only draw a string from left to right, whereas the cached layout maangers and text containers implicitly have the overhead of re-wrapping text, even if no text is wrapped ( as is the case for me ).

So, I guess I'll probably let it sit where it is. I'm only redrawing 5 times per second -- and only redrawing those strings which have changed ( rarely more than two or three ). So since it's only consuming < 1 % of CPU resources ( thanks, shark! ) I'm going to let it be.

My benchmark test is probably going overboard, having twenty or more changing and redrawing ten times per second.

--ShamylZakariya

----

Now, that's weird or not? As far as I understood, drawAtPoint creates the layoutmanager and text container from scratch everytime, and will most probably do the wrapping as well... It's only a convienience method in the end. I might be missing something but perhaps you did not implement it the way it should, did you use the CircleView example? There you layout the glyphs one for one, but that's only necessary when you want to position them all independently of each other. If all you do is draw the string the normal way, it should be even simpler and faster. Of course, if you are drawing so few and not very often, why bother indeed. 
Maybe somone with more experience (or insight knowledge) can comment?
Cheers,
Alex

----

Here's a screenshot:
http://home.earthlink.net/~zakariya/files/tracelog.gif

The screenshot shows three of my views in a flow layout. Each view shows the "trace" name and value. 

The relevant code is this:

    

- (void)drawRect:(NSRect)rect 
{	
	[_backgroundColor set];
	NSRectFill( [self bounds] );

	[_nameLayout drawGlyphsForGlyphRange: _nameGlyphRange atPoint: _nameLoc];
	[_valueLayout drawGlyphsForGlyphRange: _valueGlyphRange atPoint: _valueLoc];	
}

- (void) adopt: (TraceAtom *) atom
{
	_atom = atom;
	_atom->data = ( void * ) self;
		
	NSString *nameString = [NSString stringWithCString: _atom->name.c_str()];
	[_nameStorage setAttributedString: [[NSAttributedString alloc] 
		initWithString: nameString attributes: _nameAttribs ]];
	_nameGlyphRange = [_nameLayout glyphRangeForTextContainer: _nameContainer];

	/*
		Make sure the value is recorded, the display
		string is formatted and we're redrawn.
	*/

	[self valueChanged];
	[self formatDisplayString];
	[self display];
}

- (void) formatDisplayString
{
	if ( !_atom )
	{
		NSLog( @"[%@ formatDisplayString]\tNo _atom!", self );
		return;
	}

	NSString *valueString = nil;
	
	switch ( _atom->type )
	{
		case TraceAtom::Integer:
		{
			valueString = [NSString stringWithFormat: @"%d", _atom->intValue ];
			break;
		}
		
		case TraceAtom::Float:
		{
			valueString = [NSString stringWithFormat: @"%.3f", _atom->floatValue ];
			break;
		}

		case TraceAtom::String:
		{
			valueString = [NSString stringWithCString: _atom->stringValue.c_str() ];
			break;
		}

		default: break;
	}
	
	if ( valueString == nil ) return;
	[_valueStorage setAttributedString: [[NSAttributedString alloc] 
		initWithString: valueString attributes: _valueAttribs ]];

	_valueGlyphRange = [_valueLayout glyphRangeForTextContainer: _valueContainer];
}



Where the name is set when the view "adopts" a trace atom ( so it's layout and glyphs are calculated only once ) and the value storage and range are recalculated when the value on the trace changes.

So I'd guess the damage is in     -(void) formatDisplayString  since it needs to make a new attributed string each time. But I wasn't sure of any other good way to change the value of a string.

Probably I'm missing something since this would *seem* to be a much more direct approach with better performance.

Of course, this is not really all that important. But it's nagging me. I don't like the feeling of something not working like I expect it to because it means I'm misunderstanding something. And if I'm misunderstanding things, then implicitly the quality of my code will suffer.

--ShamylZakariya

----

You could use ATSUI; I'm not sure if CocoaDev has any pages about it. It's Apple's lower-level text API, and it's pretty fast for text that doesn't need to be rewrapped every time its drawn (hint, don't recalculate the layout when you change the text).

---- Jumping in late, as always ... 

*Another option is to simply have a timestamp for the last time a value was written and not redraw the view unless, say, 200 millis has passed. This would effectively cap my redraw to 5 times per second which is reasonable simply because my eyes wouldn't be able to make sense of 40 times per second.*

This approach is a good way to avoid paying for gratiutous UI updates while doing a lot of computation. Carbon programs have done this kind of thing for decades. I've used this approach in Cocoa progress panels as well. Of course, you need to make sure the value eventually reflects the proper value, not some stale value.

*So I'd guess the damage is in     -(void) formatDisplayString  since it needs to make a new attributed string each time. But I wasn't sure of any other good way to change the value of a string.*

Why are you guessing? If you're going to try making your code faster you **must** profile your code first. We've already been warned of pre-mature optimization earlier in this thread. Funny no? So stop guessing and fire up sample and/or Sampler. Where are you really spending your time?

*You could use ATSUI *

I'd stick with NSLayoutManager ...

-- MikeTrent

----

I know this is an old discussion, but I thought I would share this link http://www.cocoabuilder.com/archive/message/cocoa/2004/11/18/121869

--zootbobbalu

(also see NSTextViewPerformanceEnhancement and http://www.cocoabuilder.com/archive/message/cocoa/2002/11/12/7278)

