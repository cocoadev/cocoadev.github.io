---
layout: page
title: FastWritesToNSTextView
---



I'm working on an app which has a live console, into which tracing type calls from plugins is shown in real time. For reference, the app's a testbed for embodied AI in a simulated physical environment, and as such it's easier to trace what's going on if the "brains" can log what they're thinking to a more user-friendly output than stdout on a command line. http://goo.gl/OeSCu

Now, my implementation as it is works fine, it's a drawer with an NSTextView; the text view is notified when new strings are copied into a  "console" buffer ( there's a little hairiness since the console code itself is c++/STL and running in a separate thread than the GUI ). My approach is to have the text view simply figure out what new characters are present ( which is provided by the console's notification ), and append them to the text storage, and set the attributes for the new text so it's Monaco 9pt ( I prefer fixed width, for this sort of thing ). I then scroll the text view so the newest addition is visible.

The trouble is, it's really inefficient when the buffer's more than about 5000 chars long; it's bad enough that the otherwise real-time simulation and AI slow to a crawl. So far, my best approach has been to have the text view crop out "old" messages so it never gets very long. This seems less than optimal to me.

What I'd like to know is if anybody knows any tricks for making *fast* additions to an NSTextView's storage. I'm at work right now, so I don't have the offending code at hand, but if anybody's interested I'll post it all.

Here's a quick idea of how it works:

-some code, in some thread, calls      Console::print( "...", ... ) ,

-registered "listeners" to the console are notified of the new string

-an Objective-C++ glue object calls an -addString type method on my textview's controller

-my text view controller adds the string to the end of the range for the text view's storage, sets its typeface to Monaco 9, and scrolls the view down.

Am I doing something stupid? The setting of type attributes seems like a hit to me, but I tried just setting the TextView to display in Monaco 9 in IB as well as programatically but new text always showed up in Helvetica.

--ShamylZakariya

----

It probably won't be a huge gain, but if you're setting the attributes to Monaco 9 each time, you could try unchecking "Allows multiple fonts" in IB (or doing     [textView setRichText:NO] programatically) , then doing     [textView setFont: [NSFont userFixedPitchFontOfSize:9.0]] somewhere early in your code.

----

Another thing you can do is, move the brains into a different thread, and update from there. Or even use another process and communicate through an NSFileHandle like you would with stdout.

----

The brains already are in a separate thread, loaded from plugins. The GUI thread is responsible only for the OpenGL display, physics, and maintaining a simulation-time mechanism ( so time is constant, even if something slows the simulation down ). Anyway, I'll try turning off rich text... that didn't even occur to me. I've never used an NSTextView before this... 

--ShamylZakariya

----

Also turn off undo, graphics, spell checking, and everything else that sounds like it might be a speed hit.

----

Just a thought, would NSText be faster than NSTextView? Might be worth a try.

----

A possible work around might be to redisplay the view manually every half second or so. To do this you could call     [consoleView setNeedsDisplay:NO] after every change of the view (haven't tried it but I think it should work) and set up an NSTimer to call the view's     display method. Or better, you could have the NSTimer method do the scrolling down and changing of the font (if you need to change the font that is).


----

I tried various approaches, and no matter what I did, the typeface always fell back to Helvetica for new writes to the view. Quite a dissapointment. So, instead of obfuscating my code further to eke out a smidgen more performance, I've just lowered the frequency that it checks for new data. Oh well. --ShamylZakariya

----

The string you're adding - is it an NSAttributedString or just a plain old NSString? If it's an attributed string the Helvetica may be coming from there.

----

I have a similar problem -- the terrible slowdown -- only I need *some* attributes and not others.  Basically, I need only a single font, but I still need the ability to set foreground and background colors on a per-character basis (as well as the ability to set per-paragraph indents).  Reimplementing an iTerm-type terminal seems overkill -- I don't need cursor controls or anything like that -- but the slowdown is absolutely terrible after a while.  Any thoughts?

--RachelBlackman

----

Um, can't you just treat NSTextStorage like an NSMutableAttributedString (it is one) and use     appendAttributedString:. If you really are just using plain text, get the text storage's     mutableString, which is automatically tracked by the NSTextView. Then you can use     appendString:. Of course, if you are already doing it this way, I can't help you, but using     appendString: would probably solve the Helvetica problem. --JediKnil

----

To RachelBlackman: have you profiled your application? Doing that will tell you what's slow, and give you an indication of what you could make faster. For example, if it turns out that NSTextStorage is handling your attributes slowly, you could take advantage of the fact that you don't need most of them and write a custom NSTextStorage that understands the details of your application better.

*The issue is apparently in the layout system, unfortunately.  Doing     appendAttributedString: seems to cause the layout to be invalidated for the entire view; not really a problem with a smallish view, but when you're getting into large amounts of data, it's really awful.  However, since I don't need the view to be editable (only code will add to it, never the user), I have come to the reluctant (and painful) conclusion that I will need to just write my own NSView subclass that manually ties together NSTextContainer and NSLayoutManager, to ensure the adds are efficient.  Blergh. --RachelBlackman*

As an addendum: the slowdown I am experiencing is specifically when I add lines in quick succession.  At that point, it ceases to be 'slow' and can give the spinner for quite some time.  (3 seconds on a 'good' pause, 30 or more if there's literally hundreds of lines in quick succession.)  The slowdown is caused by the layout manager invalidating the layout, recalculating everything... and then doing it again (and again and again and again and again) as soon as it comes out of that process.  I can lessen -- but not eliminate -- this with a buffer that holds incoming lines, and waits until the buffer has either exceeded x number of lines or I've not received new data for .5 seconds.  The spinner goes away, but the NSTextView still reacts sluggishly. --RachelBlackman

----

If all you're after is a simple console output where you're showing plain text lines then maybe you could make a custom view which maintains an NSArray of lines (NSString), and draws each line individually - should be nice and fast. Of course then there is a bit of extra work to do depending if you also require text-selection, copy, etc. 

    
//Note: nest me within a NSScrollView - best with a background color
@interface ConsoleView : NSView {
@private
	NSMutableArray *array;
}
- (void)appendLine:(NSString*)line;

- (IBAction)test:(id)sender;

@end

@implementation ConsoleView

- (id)initWithFrame:(NSRect)frame {
    self = [super initWithFrame:frame];
    if (self) {
		array = [[[NSMutableArray alloc] init] retain];
    }
    return self;
}

- (void)dealloc {
	[array release];
	[super dealloc];
}

- (BOOL)isFlipped { return YES; } // first line at the top

- (void)drawRect:(NSRect)rect {
	int startLine = rect.origin.y/LINE_HEIGHT;
	int endLine = 1 + (rect.origin.y+rect.size.height)/LINE_HEIGHT;
	if(startLine < 0) startLine = 0;
	if(endLine > [array count]) endLine = [array count];
	int i;
	for(i = startLine; i < endLine; i++) { // only draw the changed lines
		NSString *str = [array objectAtIndex:i];
		[str drawAtPoint:NSMakePoint(0, i * LINE_HEIGHT) withAttributes:nil];
	}
}

- (void)appendLine:(NSString*)line {
	if([array count] > MAX_LINES) [array removeObjectAtIndex:0]; // limit the number of lines
	[array addObject:line];	
	int i = [array count];
	[self setFrame:NSMakeRect(0, 0, LINE_WIDTH, i*LINE_HEIGHT)]; // increase the frame size	
	[self scrollRectToVisible:NSMakeRect(0, (i-1)*LINE_HEIGHT, LINE_WIDTH, LINE_HEIGHT)]; // show the last line
	[self setNeedsDisplay:YES];
}


- (IBAction)test:(id)sender {
	[self appendLine:[sender stringValue]];
}

@end

--RbrtPntn

