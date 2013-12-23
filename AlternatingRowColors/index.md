---
layout: page
title: AlternatingRowColors
---

If you would like to alternate colors in your NSTableView, the same way as with iTunes, use something like this:

<source lang="objc">
- (void)tableView: (NSTableView *)aTableView willDisplayCell: (id)aCell 
   forTableColumn: (NSTableColumn *)aTableColumn row: (int)aRowIndex
{
    if ((aRowIndex % 2) == 0)
    {
        [aCell setDrawsBackground: YES];
        [aCell setBackgroundColor: [NSColor colorWithCalibratedRed: 0.90
                                          green: 0.90
                                          blue: 0.80
                                          alpha: 1.0]];
    }
    else
    {
        [aCell setDrawsBackground: NO];
        [aCell setBackgroundColor: [NSColor whiteColor]];
    }
}
</source>

You should also change that code to keep the color around. 

----

What, retain the colour? Why? Wouldn't the cell do that? -- RobRix

----

I believe NSTableView and NSOutlineView reuse a single cell instance
to display each row in a column. Therefore, the color would need to
be changed each time the table says it wants to render a particular
cell. -- Ben

----

They reuse a single cell instance with different values for each one? Sorry...I'm just having a bit of trouble figuring this out. I'm not sure how that would work on the inside...

Thanks,

-- RobRix

----

The table uses the same cell object to draw every line. Therefore, you need to explicitly modify it for each iteration, or it will use the last setting.

-- chmod007@mac.com

----

Even simpler: set the background color for the cell once (- (void)windowDidLoad is a good place for that). Then it's just a matter of switching the background drawing on and off.

<source lang="objc">
- (void)tableView: (NSTableView *)aTableView willDisplayCell: (id)aCell 
   forTableColumn: (NSTableColumn *)aTableColumn row: (int)aRowIndex
{
    [aCell setDrawsBackground: ((aRowIndex % 2) == 0)];
}
</source>

-- Tobias Lidskog (tobli176@student.liu.se)

----

At least when I compiled that code, there was a border around the cell's kind of took away from the Aqua-ness of my interface, so I popped this code into my document:

<source lang="objc">
- (void)windowControllerDidLoadNib:(NSWindowController *) aController
{
    NSSize newSize;
    [super windowControllerDidLoadNib:aController];
    newSize.width = 0;
    newSize.height = 0;
    [myTableView setIntercellSpacing:newSize];
}
</source>

That way it looks like the entire row is a single bar, not disparate units. Now you won't know that my table's made of little NSCells when you use it.

----

OK, here's one I've had around for a while.  Assume similar code to the above to give outline view alternating colours, with intercell spacing set to 0.  How do I get the background to draw under the disclosure triangle and in the left indentation as well?

It should be noted, I'm not sure that it will look any good, I just wondered if it could be done ;-)

By the way, here's some code to get outline views to change colour only on a change of top-level item.  Assume self responds to a selector -objectArray which returns an array of the top-level objects displayed in the outline view, and in the right order.

<source lang="objc">
- (void) outlineView: (NSOutlineView *) aView
     willDisplayCell: (id) aCell
      forTableColumn: (NSTableColumn *)aColumn
                item: (id) anItem
{
  id rootObj = anItem;
  unsigned row = [aView rowForItem:anItem];

  [aCell setDrawsBackground: YES];

  while ([aView levelForRow:row] != 0) {
    row --;
    rootObj = [aView itemAtRow:row];
  }

  // The colours here are foul and ugly.  Use something else, for
  // God's sake!
  if (self objectArray] indexOfObject:rootObj] % 2)
    [aCell setBackgroundColor: [[[NSColor yellowColor]];
  else
    [aCell setBackgroundColor: [NSColor blueColor]];
}
</source>

TufTy

----

Unfortunately, these only work if all the cells are text cells. NSImageCells, for example, have no setBackgroundColor: method.  Does anyone know a way to implement alternating row color that works with any NSCell subclass?

Fergy

----

All these ideas work great and all, but they don't cover the entire table view the way iTunes does. It look a mite silly when you only have 3 or 4 items. How exactly could you get around this problem? Is subclassing the only solution?

-- DaveFayram

----

All this is very nice indeed, but one thing is missing: the highlighting, when in iTunes, the rows are highlighted in a nice dark blue. In my app, everything is highlighted in a dull grey... any ideas?

- TheoHultberg/iconara

NSTableView has a handy looking -isrowSelected: selector that could well be useful for this.  Otherwise, look into NSColor's +selectedControlColor selector.

----

Wouldn't it be possible to do this by drawing a custom background for the view? This way you would get line colors for the whole view, and they would not contain blank spaces between each cell even with intercellspacing. If someone could enlighten me on how to draw behind the views GUI elements, I'd try to implement it.

-FrodeDanielsen

----

Yes it's right, we must to it, because actually we just put a color in row where there is something, a raw with any text has no background :-(

--Cl�ment Wehrung

----

This discussion should become largely moot. According to sources, the new OS X development environment will give us these textured looks for free, alternating row colors included. However, it might be interesting to figure out how to do this explicitly, to be able to selectively control row colors in distinct ways for tables and outline views for more feedback.

-- DaveFayram

----
Hey Dave, which development environment are you talking about??

-BobC

----

I'd guess that would be either InterfaceBuilder or even just integral support in the appropriate NSView subclasses -- RobRix

----

There isn't any inbuilt functionality like this in Jaguar from my investigations, so I spent a little time incorporating some fixes to Bradford Bryant's ColoredRowTableView object, and making it more iTunes-y. You can get the object from http://boomBalada.fateback.com/

-- TonyArnold

----

The download link on your website doesn't seem to work. The tableview looks neat, so could you please try and fix it ASAP :) ? -- Ibson

----

I was looking at some sample code on Apple's dev site (for their simple mp3-player application at http://developer.apple.com/samplecode/Sample_Code/Cocoa/MP3_Player.htm ), and lo and behold, they included code for the alternating stripes!  You have to subclass NSTableView and add the following code to the subclass:

Before @implentation:
<source lang="objc">
// RGB values for stripe color (light blue)
#define STRIPE_RED   (237.0 / 255.0)
#define STRIPE_GREEN (243.0 / 255.0)
#define STRIPE_BLUE  (254.0 / 255.0)
static NSColor *sStripeColor = nil;
</source>

After @implentation:
<source lang="objc">
// This is called after the table background is filled in,
// but before the cell contents are drawn.
// We override it so we can do our own light-blue row stripes a la iTunes.
- (void) highlightSelectionInClipRect:(NSRect)rect {
    [self drawStripesInRect:rect];
    [super highlightSelectionInClipRect:rect];
}

// This routine does the actual blue stripe drawing,
// filling in every other row of the table with a blue background
// so you can follow the rows easier with your eyes.
- (void) drawStripesInRect:(NSRect)clipRect {
    NSRect stripeRect;
    float fullRowHeight = [self rowHeight] + [self intercellSpacing].height;
    float clipBottom = NSMaxY(clipRect);
    int firstStripe = clipRect.origin.y / fullRowHeight;
    if (firstStripe % 2 == 0)
        firstStripe++;   // we're only interested in drawing the stripes
                         // set up first rect
    stripeRect.origin.x = clipRect.origin.x;
    stripeRect.origin.y = firstStripe * fullRowHeight;
    stripeRect.size.width = clipRect.size.width;
    stripeRect.size.height = fullRowHeight;
    // set the color
    if (sStripeColor == nil)
        sStripeColor = [[NSColor colorWithCalibratedRed:STRIPE_RED
                           green:STRIPE_GREEN
                           blue:STRIPE_BLUE
                           alpha:1.0] retain];
    [sStripeColor set];
    // and draw the stripes
    while (stripeRect.origin.y < clipBottom) {
        NSRectFill(stripeRect);
        stripeRect.origin.y += fullRowHeight * 2.0;
    }
}
</source>

I haven't tried this yet, but it looks promising!  -- JackNutting

----

Oops, sorry...try again now - it should work... 
http://www.iinet.net.au/~tonyarnold/

Apple's code looks like it might draw quicker than mine, but it doesn't do the highlighted rows properly, nor does it seem to draw the vertical gridlines like iTunes does. My code also reflects the current system highlight color. I'll be posting an update tonight (3rd September, 2002) that introduces some faster drawing code, memory optimisations and more authentic grid drawing in the tableView. I guess the next step is work on porting this to NSOutlineView (shouldn't be too difficult).

-- TonyArnold
----

Update (20030104): Me and my big mouth....my brain hurts...NSOutlineView is eeeevil...

----

Anyone figure out how to get the grid lines to draw properly with the stipes?  Specifically, make the gird lines draw properly when you resize the columnes?  I've got the following code that draws the grid lines, but when you resize a column only the rows that have items in them get updated (rows that have no items are not updated).

Update (4/7/03) - I've finally found the solution to getting the grid lines to draw properly.  Use the code below in your subclass of NSTableView or NSOutlineView:

<source lang="objc">
// Draw the vertical lines.
- (void)drawGridInClipRect:(NSRect)rect
{
	NSRange columnRange = [self columnsInRect:rect];
	int i;
	[[NSColor lightGrayColor] set];

	for (	i = columnRange.location ;
			i < NSMaxRange(columnRange) ;
			i++ )
	{
		NSRect colRect = [self rectOfColumn:i];
		int rightEdge = (int) 0.5 + colRect.origin.x + colRect.size.width;
		[NSBezierPath strokeLineFromPoint:
				NSMakePoint(-0.5+rightEdge, -0.5+rect.origin.y)
			toPoint:
				NSMakePoint(-0.5+rightEdge, -0.5+rect.origin.y + rect.size.height)];
    }
}

- (void)awakeFromNib
{
	//	this is to work around a bug with grid lines (possibly in Interface Builder)
	//	apparently you must explicitly turn them off and then back on again.
	[self setDrawsGrid:NO];
	[self setDrawsGrid:YES];
}
</source>

Because of a bug in Interface Builder (or possibly elsewhere) you need to explicitly turn off and then on the grid lines.  Using this code along with the rest of the code here will give you alternating row colors and grid lines just like in iTunes.  Happy coding!

-- DaveThorup

----

Tony Arnolds code is good, but the

<source lang="objc">- (void)tableView:(NSTableView *)aTableView setObjectValue:(id)anObject forTableColumn:(NSTableColumn *)aTableColumn row:(int)rowIndex</source>

method for the data source does not work.

Any hints ?

Thx
Chris

----

Please be more specific than "does not work". There's no way to know your expectation of what  "work" means in context, and you don't say what did happen when you tried.

In other news, Evan Jones solved the problem of alternating row colors in an NSOutlineView: <http://www.eng.uwaterloo.ca/~ejones/software/osx-iappview.html>. It's a simple, brilliant subclass hack.

Nat Irons

----

I've recently updated my code above with the solution for getting the grid lines to work properly.  Check it out. 

Nat, I checked out the iAppViews and they don't work very well for me.  First, they don't extend the alternating row colors past the data in the table.  For example, if I only have 2 items in the table, the first will be blue and the second white, but past that there will be white space.  If you use the code provided here you'll get alternating rows even where there aren't any items in the table. 

Second, the highlighting is not correct when a cell is being edited and the application is in the background.  When the app is in the background a selected row should appear gray.  With iAppViews the row does not appear grey when you are editing a table item.  This is another problem that is avoided when you use the code provided here. 

Thanks for everyone that's contributed here.  We now have a good implementation for alternating row colors. 

-- DaveThorup 

This posting spellchecked with HyperSpell .

----

OK, thanks to the hard work of Dave Thorup, the iTableView class works perfectly! You can grab it from http://www.iinet.net.au/~tonyarnold/archives/000015.html. I should have some time to move the work done here across into an identical OutlineView in the next week or so. Problem solved... (finally!) :)

TonyArnold

----

You guys rock.  Now I've got to go through the code and actually learn something about how you managed it...

NateClinton

----

It appears that editing an iTableView cell fails to set the text color to black before editing begins.. I end up editing invisible (white on white) text.  This seems to be a side-effect of turning the highlighted selection white; is this a problem specific to me, or is anyone else experiencing this?

I've tried hacking the color change with delegates and notifications to grab things just before editing begins, with no luck.

MatthewSwann

----

Yeah, somebody pointed that out to me on my blog a while back... I don't have time to fix it now, but hopefully early next week.

TonyArnold

----

Did some bug fixing to the view, by others a particularily nasty one that would crash the dealloc. (NSTableView's dealloc apparently sets datasource to nil, coupled with the method for getting information from the datasource in setDatasource: this could get ugly).

http://homepage.mac.com/atotland/.cv/atotland/Public/OpenSource/iTableView.tgz-link.gz

AndersTotland

----

Last time I tried your iTableView, it bugged out on cells that were not text field cells, if you haven't fixed that, you should. I rewrote your code thourougly for my app (look up FeedMe at versiontracker), to make it more general and to draw background colour in cells on the selected line which are being edited. -- TheoHultberg/Iconara

----

Yep. I've abandoned the iTableView for the foreseeable future. Apple has done a really good job with the 'Alternating Rows' feature in 10.3, so I didn't see any point in continuing (I only ever used this for personal projects, which I am quite happy to move to 10.3 - plus, I really was blundering about with no idea of what to do *grin*)

TonyArnold

