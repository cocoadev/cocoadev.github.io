---
layout: page
title: NSTableViewRollover
---

An NSTableView subclass which allows for row highlighting on mouseover (a la NewsFire).

Interface & Implementation:

    
@interface ROTableView : NSTableView
{
	NSTrackingRectTag trackingTag;
	BOOL mouseOverView;
	int mouseOverRow;
	int lastOverRow;
}
@end

@implementation ROTableView
- (void)awakeFromNib
{
	self window] setAcceptsMouseMovedEvents:YES];
	trackingTag = [self addTrackingRect:[self frame] owner:self userData:nil assumeInside:NO];
	mouseOverView = NO;
	mouseOverRow = -1;
	lastOverRow = -1;
}

- (void)dealloc
{
	[self removeTrackingRect:trackingTag];
	[super dealloc];
}

- (void)mouseEntered:([[NSEvent*)theEvent
{
	mouseOverView = YES;
}

- (void)mouseMoved:(NSEvent*)theEvent
{
	id myDelegate = [self delegate];

	if (!myDelegate)
		return; // No delegate, no need to track the mouse.
	if (![myDelegate respondsToSelector:@selector(tableView:willDisplayCell:forTableColumn:row:)])
		return; // If the delegate doesn't modify the drawing, don't track.

	if (mouseOverView) {
		mouseOverRow = [self rowAtPoint:[self convertPoint:[theEvent locationInWindow] fromView:nil]];
		
		if (lastOverRow == mouseOverRow)
			return;
		else {
			[self setNeedsDisplayInRect:[self rectOfRow:lastOverRow]];
			lastOverRow = mouseOverRow;
		}

	[self setNeedsDisplayInRect:[self rectOfRow:mouseOverRow]];
	}
}

- (void)mouseExited:(NSEvent *)theEvent
{
	mouseOverView = NO;
	[self setNeedsDisplayInRect:[self rectOfRow:mouseOverRow]];
	mouseOverRow = -1;
	lastOverRow = -1;
}

- (int)mouseOverRow
{
	return mouseOverRow;
}

- (void)viewDidEndLiveResize
{
        [super viewDidEndLiveResize];

        [self removeTrackingRect:trackingTag];
        trackingTag = [self addTrackingRect:[self frame] owner:self userData:nil assumeInside:NO];
}

@end



And the delegate...

    
- (void)tableView:(NSTableView *)aTableView willDisplayCell:(id)aCell forTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex
 {
	if ([aTableView mouseOverRow] == rowIndex)
		NSLog(@"%d could be highlighted", rowIndex);
	else NSLog(@"%d shouldn't be highlighted", rowIndex);
 }


----

I find this whole 'feature' vaguely disturbing. I hope it doesn't become common.

*Reminds me of XP's "hot tracking' feature. bleh*

----

I could see this being useful.  Is there an app out there that uses this code? 

Sort of -- it's called NewsFire

*It was done in the edit before yours (on the 26th) so I doubt anything uses this exactly but the general idea probably is used somewhere.*

----
And here's a another delegate that draws the ITunesStyleGradient under the row the mouse is over. Should probably track the controlTint but you can add that if I don't at some point.

    
NSString *highlightImageData = @"<4d4d002a 00000048 800f4f6d a2ca65ca 564b390a 69371941 1ee22622 dc04743b 7c86826e 900fcdb1 d9e5b237 3ab60647 06b0b8d8 d5151a1a 82732348 46616888 4bcd00f9 719f0100 000d0100 00030000 00010001 00000101 00030000 00010012 00000102 00030000 00030000 00ea0103 00030000 00010005 00000106 00030000 00010002 00000111 00040000 00010000 00080115 00030000 00010003 00000116 00040000 00010000 2aaa0117 00040000 00010000 003f011a 00050000 00010000 00f0011b 00050000 00010000 00f8011c 00030000 00010001 00000128 00030000 00010002 00000000 00000008 00080008 000afc80 00002710 000afc80 00002710 >";
NSImage *highlightImage;

- (void)tableView:(NSTableView *)aTableView willDisplayCell:(id)aCell forTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex
 {
	if ([aTableView mouseOverRow] == rowIndex && ([aTableView selectedRow] != rowIndex))
			if ([aTableView lockFocusIfCanDraw]) {
				NSRect rowRect = [aTableView rectOfRow:rowIndex];
				NSRect columnRect = [aTableView rectOfColumn:aTableView tableColumns] indexOfObject:aTableColumn;

					if (!highlightImage) {
						highlightImage = [[NSImage alloc] initWithData:[highlightImageData propertyList]];
							[highlightImage setDataRetained:YES]; //?
							[highlightImage setCacheMode:NSImageCacheAlways]; //?
					}

				[highlightImage drawInRect:NSIntersectionRect(rowRect, columnRect) fromRect:NSMakeRect(0,0,1,[highlightImage size].height) operation:NSCompositeSourceOver fraction:0.3];
				[aTableView unlockFocus];
			}
 }

- (void)dealloc
{
  if (highlightImage) [highlightImage release];
[super dealloc];
}


Added     viewDidEndLiveResize so the tracking rect keeps up with the table's frame. Also, if your table has more than one column the current delegate doesn't highlight properly, it draws over some rows instead of under them. I'm not sure how to fix that yet so if you've got an idea please share.

*Nevermind, fixed that too. It turns out I'd had the solution a couple times already but didn't realize it - and compared to what I had before, this one is pretty succinct. Now I wonder if I should *scratch* draw something special if the column is selected...hmm...*

----

I have to agree that this is ugly, nonstandard behavior--reminiscent of Windows. I hope it does not get used much, to be quite honest. FYI, NewsFire uses WebKit and CSS to create the tables (at least it did in earlier versions, I haven't checked recently) which also strikes me as a rather unnecessary hack. --RobinHP

*See the first example delegate. Perhaps on a mouse-over you display more info about a row instead of drawing the provided highlightImage - who knows, it's a blank slate. That said, you could easily modify the second delegate to suit your needs since it just draws an image under the row the mouse is over.*

----
> this is ugly, nonstandard behavior--reminiscent of Windows

I can think of one instance where (I think) this would be quite useful - spreadsheets.  For the same reasons that well designed/printed tables have alternatly colored rows (it's easier for the eye to follow them and not get lost)..  Which I believe is one of the places Windows uses it..  It should be an option of course, or even better - hit a modifier key and get the behavior.  - Jim

----
One place I'd like to see something like this used is in iTunes. Imagine if you could mouse over your playlists and see the average rating of the songs contained therein. It's not vital information by any means but as an added touch, it could be cool. That's the kind of situation I would use it in, anyway.

----

I've always thought that some apps start to feel clunky - like Keynote, for example, when they lack visual feedback. I wish Apple would offer an option to see that sort of behavior. If there was one app that I could tolerate from M$, it would be PowerPoint, because it does this sort of thing. PowerPoint 2003 feels light and reactive, even on a 700 MHz Pentium 3, whereas Keynote feels sluggish on my G5. Of course, this is my perception, but I've used PowerPoint for a long time, and I loved the switch up from 2000 to 2003, but using the Mac version is excruciating! What does everyone else think?

*Get more RAM :-)*

Even with 2 GB of RAM, Keynote is a turtle in terms of user interaction, so that's probably not the issue.

----
Shouldn't we add     mouseOverColumn to kind of round this out?
----

Some fixes:

Move all calls to addTrackingRect to -resetCursorRects. Then, whenever the frame changes, your tracking rect will be updated. Do this instead of riding on viewDidEndLiveResize.  Further, instead of addTrackingRect:[self frame], you should addTrackingRect:[self visibleFrame]. Otherwise, there's a danger of tracking rects overlapping because you're not taking account parent view clipping.

----
Mouse Over animations are only necessary when it is not obvious that the interface widget responds to a click, drag or other type of action. Or that you are providing auxiliary information about the object in question, ex. Tooltips Dock.app. Good examples of no-no mouse overs are Adobe PDF Reader and Microsoft Word, NewsFire at least does a good looking job, but nonetheless it is unnecessary. We're not building Web pages here.

----
Mouse Over animations are useful in general to provide added feedback that your pointer is over the hittable area of a widget. Your standard Mac OS X menus, for example. The menu items they contain are obviously clickable, yet rolling over them produces a roll-over effect. Specifically, they are useful in adding graphical affordance to elements that otherwise don't have them. Table views don't have any more graphical affordance than menu items do.

----
I am trying to use this method of tracking the mouse over row, but I'm having a problem in that the mouseMoved event never reaches my view.  I am definitely getting mouse entered/exited events, but not the moved events.  I am using the orignal example verbatim. Can anyone think of a reason why this would occur?  


----
Well, I recall not getting mouseMoved events at times when different views were first responder. 

However, NSTrackingArea (OS X 10.5) is another way to do this, without subclassing. Your controller (the delegate of the tableview) would have a similar mouseOver method that calls -rowAtPoint:. See sample code TrackIt.



 ----
One other even more flexible way is to create a custom NSView, override drawRect: 
Than create and use an instance.

    
ATFontSampleView *sampleView;

- (void)tableView:(NSTableView *)aTableView willDisplayCell:(id)cell forTableColumn:(NSTableColumn *)aTableColumn row:(NSInteger)rowIndex {    
    NSLog(@"<%@ 0x%lx %@>", NSStringFromClass([self class]), (NSInteger)self, NSStringFromSelector(_cmd));    
    if ([aTableView mouseOverRow] == rowIndex && ([aTableView selectedRow] != rowIndex)) {
        if ([aTableView lockFocusIfCanDraw]) {
            NSRect rowRect = [aTableView rectOfRow:rowIndex];
            NSRect columnRect = [aTableView rectOfColumn:aTableView tableColumns] indexOfObject:aTableColumn;
                        
            if (!sampleView) {
                sampleView = [[ATFontSampleView alloc] initWithFrame:rowRect];
            }
            
            [sampleView drawRect:rowRect];
            [aTableView unlockFocus];
        }
    }
}

