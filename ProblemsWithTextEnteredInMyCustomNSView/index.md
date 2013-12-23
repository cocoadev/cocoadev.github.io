---
layout: page
title: ProblemsWithTextEnteredInMyCustomNSView
---



I am having a problem with an NSTextView subclass.
When you type text into the textview in the running application, the text appears just as odd patterns of vertical lines, sort of like this...
" |||  ||| | ||   | | | "
Even spaces and carriage returns cause the same thing.
If you select the text you get no indication that it had selected, but dragging from the view to the desktop shows the actual text and it will paste into any normal text field and disply normally.
My suspicion is that I am missing something that I must override in a custom NSTextView, but I can't see what.
I'm not sure how helpful a code example would be but here it is:
    
#import "CDTextView.h"


@implementation CDTextView

- (id)initWithFrame:(NSRect)frame {
    self = [super initWithFrame:frame];
    if (self) {
        // Initialization code here.
		[self setEditable:YES];
		[self setBackgroundColor:[NSColor blackColor]];
		[self setRichText:YES];
		NSFont *padFont = [NSFont fontWithName:@"Helvetica Neue Bold" size:20];
		[self setFont:padFont];
		[self setUsesFontPanel:YES];
		[self setUsesFindPanel:YES];
    }
    return self;
}

- (BOOL) becomeFirstResponder
{
	return YES;
}


- (void)drawRect:(NSRect)rect
{
	//New code to draw the lines grid
	int rectWidth = rect.size.width; //gets the width of the custom view in pixels
	int rectHeight = rect.size.height; //gets the height of the custome view in pixels
	int divider = 25; //how many divisions do you want the page divided up into? set it here
	int countY = rectHeight/divider; //...and likewise for the height
									 //now we know the size of the view and how many times to draw a line in the x & y axis of the view
									 //the nextstep (see what I did there! ) is to draw the lines...
	
	NSBezierPath *gridLine = [NSBezierPath bezierPath]; //Create an NSBezierPath
	NSColor *gridColor = [[NSColor grayColor]colorWithAlphaComponent:0.5];//Set the color and the alpha channel for the lines
		[gridLine setLineWidth:0.7]; //set the line width
		[gridColor set];
		int j;
		int coordY = divider;
		for (j = 0; j <= countY; j++) {
			[gridLine moveToPoint: NSMakePoint(0,coordY)];
			[gridLine lineToPoint: NSMakePoint(rectWidth,coordY)];
			//[gridLine stroke];
			coordY = coordY + (divider - 1);
		}
		[gridLine stroke];
}

@end


----
Your "odd pattern of vertical lines" is the grid that you're drawing....

And you're not getting any text because you aren't calling super.

----

What (s)he said. But also, your code contains a glaring misunderstanding - the rect passed to drawRect: is NOT the size of the view in pixels as your comments suggest - it is the area of the view needing update. Basing your grid on this will cause it to draw where the textview is trying to redraw a few characters, hence the effect you see. What you need to do is base your grid on     [self bounds]; then call     [super drawRect:rect]; to draw the text on top. --GrahamCox

----
For more details on how to use this parameter, see the oddly appropriately named DrawRect.

----
Hmmm. Cheers for the asssitance, I have the text working fine now I have called super for drawRect. Seems the vertical lines were caused by this NOT by drawing the grid.
Herein lies an issue.. I have the textview working nicely now. However I still need to draw the lines to simulate the notepad look. I can't do it in the custom view as that doesn;t show through the TextView. Sending the TextViews alpha channel to zero just makes it totally clear all the way through the app so it's a big empty square. I made a method in the Text View to draw the lines but it doesn't appear to do anything. Sigh. I'll try calling it in awakeFromNib. But any (non sarcastic) suggestions are gratefully received. I'm new to this and not very learned yet.

----
Maybe     -setDrawsBackground can help you? You'd lose the capability for background colors but you'd get the grid lines. --JediKnil

----

Your comment : "Seems the vertical lines were caused by this NOT by drawing the grid" MUST be wrong. The only drawing code that was being done was drawing grid lines (aligned to the incorrect update rect, not the view bounds). When text is typed, the update rect will be kept to a minimum (drawing text is very expensive), so will usually only be the bounds of a character or two. This is passed in the <rect> parameter. Your grid code was using this as the view edges and drawing lines there, so the grid lines appear to line up at the typed character positions. The symptoms shown and the given code agree perfectly. The next problem is that NSTextView erases the background behind characters to the background colour by default, which of course will also erase anything you've drawn there. You need to turn of the text view's erasing as suggested above. However, then you may run into other problems - text characters drawn without an erase will not anti-alias properly with repeated redraws, so you will need to take on the erase yourself - this will be the first thing your code should do - erase <rect> to the background colour, then draw the grid based on <bounds>, then call super to draw the text. You should find that will work as you want. --GC

----

(sigh) Yet another would-be community member using the wiki in MailingListMode ...

----

Look, who cares? The point of this site is to get help and solutions to problems. I personally find that while presenting a complete solution is perhaps ideal, the real world doesn't work that way - you try something, it maybe partially works, but you don't fully understand - you get a little bit more help, and so on, until it's there. That's the process by which much coding is done, like it or not. I find that I can often get just as much use out of pages in "mailing list mode" as the rarer complete solutions. So the site serves its purpose either way. How many of us are in the habit of coming back and reformatting a page to present only the solution? Not many - and often the process that led to a solution is just as worthwhile as the solution itself. I think some people just like to go around acting the frustrated security guard - "rules is rules!!". Well, that other great well-known Wiki effort is now so stuffed full of those people it's hard to get anything done. So let's quit carping here, eh? --GC

----
I agree with you..There are some people those who just know to say HowToAskQuestion , MailingListMode. For each and every question appeared in this page they put  HowToAskQuestion without actually trying the answer the question. It simply spoils the page or it leads to unwanted discussion(like this) See..this is wiki site, if any one could not understand the question just forget the person who asked the question or ask him what extra info u want.

----

Have either of you *read* the MailingListMode and HowToAskQuestions pages? This is not a forum. This is meant to be a living reference book and people who post things as if they're using a mailing list are basically scribbling their own notes all over a text book used by other students. It's rude and it poor etiquette. Act a fool and you'll be treated as one.

----

And have you noticed that this very page we are editing here lies within the "Discussions" section, which is opened by the remark: 

"This page is a jumping-off point for anyone wishing to enter a free-form discussion about some aspect of Cocoa, or Mac OS X programming."

FREE-FORM discussion says everything, doesnt it? Isnt this the place to really discuss problems, rather than offering text book like entries? I agree in that the entries on defined cocoa topics (e.g. "NSTextView" or the like) should really be in wiki text book style and discussions over there are distracting and counterproductive. Overe here, however, I find them quite helpful in the way they are held at the moment!

--marcocoa

----
Whoa...
I'm kinda regretting asking a question in the first place. In support of people above, I did kinda think that an area called Discussions might be just that. Now it's true I have a sort of "Ignore the disclaimer, just click OK" attitude and maybe RTFMing might help but surely this is a place to discuss an issue and I would be happy to reformart it into a complete solution once I have one. Which I don't yet...
So here's my take: The ONLY thing I changed from the inital code was to call [super drawRect:frame];
This fixed the problem of the text coming out as vertical lines.
I then disabled ALL the grid code and then removed the super call and the lines returned. They were the | character not just drawn lines like in the grid code anyway.
I'll keep on keeping on in the meantime and try to get it all working as I want it.

----
The call to super is 100% necessary so talking about what happens when you take it out is pretty much useless anyway.

----
OK, I don't really know, what you were doing, but with the suggestions from above, everything should work nicely. I put together some code (basically yours adjusted a bit), here it comes:

    
- (void) awakeFromNib {
	NSLog(@"awaking");
	[self setEditable:YES];
	[self setDrawsBackground:NO];  // NOTE THIS ONE!
	[self setRichText:YES];
	NSFont *padFont = [NSFont fontWithName:@"Helvetica Neue Bold" size:20];
	[self setFont:padFont];
	[self setUsesFontPanel:YES];
	[self setUsesFindPanel:YES];
}
	
	
	
- (void)drawRect:(NSRect)rect {
	
	NSRect bounds = [self bounds];  // NOTE THIS ONE, TOO
	
	int rectWidth = bounds.size.width;       //NOT rect BUT bounds!!!!!
	int rectHeight = bounds.size.height;    // NOT rect BUT bounds!!!!!
	int divider = 25;                                 //how many divisions do you want the page divided up into? set it here
	int countY = rectHeight/divider;         //...and likewise for the height
	
	NSBezierPath *gridLine = [NSBezierPath bezierPath];  //Create an NSBezierPath
	NSColor *gridColor = [[NSColor grayColor]colorWithAlphaComponent:0.5];  //Set the color and the alpha channel for the lines
	[gridLine setLineWidth:0.7];  //set the line width
	int j;
	int coordY = divider;
	for (j = 0; j <= countY; j++) {
		[gridLine moveToPoint: NSMakePoint(0,coordY)];
		[gridLine lineToPoint: NSMakePoint(rectWidth,coordY)];
		coordY = coordY + (divider - 1);
	}
		
		
	[[NSColor whiteColor] set];  //OR WHATEVER BACKGROUND YOU WISH
	NSRectFill(bounds);             // CLEARING THE VIEW
		
	[gridColor set];
	[gridLine stroke];
	
    [super drawRect:rect];           // MUY IMPORTANTE!!!!
}


I have this in an NSTextView subclass and everything works like a charm (I instantiated the view in IB)... And, oh, yeah, I d put the Bezierpath stuff into a separate method and just recalulate the path whenever it changes. In the draw method, simply call [myPathAsAnInstanceVariable stroke]...

Possible though, that I completely misunderstood your problem and this is not what you desired. If so, tell us!

--marcocoa

----

"... This is meant to be a living reference book...". Says you. But I reiterate - surely the point here is to help people understand and therefore be productive using Cocoa. What's YOUR motivation? Just to tell people how to behave? Mine isn't, it's to help share any useful knowledge I have, for the general benefit of the Mac and Cocoa. I don't really care how it happens, as long as it does. It's not like there's any limit on the number of pages you can add to a Wiki. Maybe there should be a separate index for "mailing list" type sessions, because the point is, it works; people get something from them. When you need to tell 10,000 people they're "doing it wrong", then surely it's time to realise that actually what's needed is a better way to accommodate the 10,000, not to force those people into another mode of communication which clearly isn't natural for the situation at hand. --GC

----

Let's see here:

"The ONLY thing I changed from the inital code was to call [super drawRect:frame];"

Do you mean frame? Or rect? frame would be definitely wrong. You really need to understand what is meant by frame, bounds and the update rect passed to drawRect to properly implement views. If you are confused (which appears to be the case) sort it out in your head before proceeding.

"I then disabled ALL the grid code and then removed the super call and the lines returned. They were the | character not just drawn lines ..."

How can this logically be true? If nothing is doing any drawing, then why is anything appearing? Where is it coming from? Why would anything be drawing a | character? Views are not magic - everything has a cause. If there's something you see that you don't understand, don't assume - find out. If you override drawRect and it does nothing, nothing will be drawn, simple as that. If something is appearing, then something is drawing, ergo, it must be code in your view. In this case there is one other possibility - what you're seeing is in fact unerased remnants of the insertion point. Is it? Find out. --GC

----
All good points, but I have a minor nit: it's possible that NSTextView does drawing outside of drawRect: by using lockFocus, so it is possible that something is drawing even if you override drawRect: and make it do nothing. In fact I wager that the insertion point is drawn in this fashion, but is erased by invalidating the view, which is why you get a bunch of |'s showing up.

----
Just to throw in: Thats correct, the insertion point is drawn outside of the drawRect: method- which makes sense as it is not literally part of the view's contents, is it. However, as I have set up the above mentioned sample (which nicely draws the text with a "grid" of lines below), I tested to override the drawRect: method to do nothing and: it does nothing! The insertion point is drawn and even moves according to the text you type, just nothing at all is drawn inside the view, no grid, no text, just as it is supposed and logic to be.

To the original poster: one more point to your initial code- be aware that init methods are not called upon app start-up for a view that you instantiate in IB! Therefore you need to move that stuff into awakeFromNib:...

And one more note to you: if people post a dedicated solution / answer to a problem / question here on cocoadev, you shouldn�t really go about arguing that it doesn�t work or it�s non-sense or so. Those people really know what they are doing in 98% of all cases (and in this particular case, they even know what you are doing better than yourself), trust me! No problem if you still don't get it to work, but then don't blame the ones that wanted to help you but frankly admit: "I can't do it, could anyone please show me how it is done?"

--MarCocoa

