---
layout: page
title: SpeedingUpADrag
---



I'm learning drag and drop now. I have several subviews (of the same class) in a view. I can drag these subviews using this code :

    
- (void)mouseDown:(NSEvent *)event
{
	if (self window] firstResponder] == self)
	{
	[[NSPasteboard *pboard =[NSPasteboard pasteboardWithName:NSDragPboard];
	NSData *data = [self dataWithPDFInsideRect:[self bounds]];
	NSImage *image = [[NSImage alloc] initWithData:data];
    [pboard declareTypes:
		[NSArray arrayWithObjects:NSPDFPboardType,
			NSStringPboardType,nil] 
				   owner:self];
	[self dragImage:image
				 at:NSMakePoint(0,0)
			 offset:NSMakeSize(0,0)
			  event:event
		 pasteboard:pboard source:self
		  slideBack:YES];
	[image autorelease];
	} else {
		self window] makeFirstResponder:self];
	}
}


But it's very slow. The first drag takes 6 seconds to make the "PDF" copy and drag it. Then it's faster, less than 1 second, even if i drag another subview.

How can i improve this process? - [[StephaneDassieu

----

Can you provide a bit more information about the custom view you're drawing? It's most likely a performance issue in your drawing code or in the level of complexity in your view. Perhaps if you post your     -drawRect: code.

----

ahh damn.. my     -drawRect: code is pretty big... here a resumed version:

    

	int boxwidth = self superview] bounds].size.width -marge;
	int top = [self bounds].origin.y +[self bounds].size.height -(curve*2) -marge;
	int height = [self bounds].size.height -(curve*2) -(bottomheight +curve) -marge -marge ;
		
	//=== top ===
	[[NSBezierPath *pathTop = [NSBezierPath bezierPath];
	// cut ->4 bezier operations
	[pathTop closePath];
	
	//=== closebox ===
	closeBoxBounds = NSMakeRect(boxwidth-13-marge+2, top+3+3, 13, 13);
	if (!closeBox)
	{
		closeBox = [[NSButton alloc] initWithFrame:closeBoxBounds];
		[closeBox setButtonType: NSMomentaryChangeButton];
		[closeBox setBordered: NO];
		[closeBox setAction: @selector(closeBloc)];
		[closeBox setTarget: self];
		
		NSImage *BoxButtons = [NSImage imageNamed: @"blocs-buttons"];
		NSImage *closeBoxImage = [[NSImage alloc] initWithSize:NSMakeSize(13, 13)];
		NSImage *closeBoxImagePressed = [[NSImage alloc] initWithSize:NSMakeSize(13, 13)];
		
		[closeBoxImage lockFocus];
		[BoxButtons compositeToPoint:NSZeroPoint fromRect:NSMakeRect(52, 39, 13, 13) operation:NSCompositeCopy];
		[closeBoxImage unlockFocus];
		[closeBoxImagePressed lockFocus];
		[BoxButtons compositeToPoint:NSZeroPoint fromRect:NSMakeRect(52, 26, 13, 13) operation:NSCompositeCopy];
		[closeBoxImagePressed unlockFocus];
		
		[closeBox setImage: closeBoxImage];
		[closeBox setAlternateImage: closeBoxImagePressed];
		[self addSubview: closeBox];
		[closeBox release];
		[closeBoxImage release];
		[closeBoxImagePressed release];
	} else [closeBox setFrame:closeBoxBounds];
	
	//=== editbox ===
	editBoxBounds = NSMakeRect(marge+indexdiameter+4+4, top+3+3, 13, 13);
	if (!editBox)
	{
	//cut -> almost the same as above (closebox)
	} else [editBox setFrame:editBoxBounds];
	
	//=== index ===
	NSRect circleNumberBounds = NSMakeRect(marge+4, top+4, indexdiameter, indexdiameter);
	NSBezierPath *pathCircleNumber = [NSBezierPath bezierPathWithOvalInRect:circleNumberBounds];
	string = [NSString stringWithFormat: @"%@", index];
	
	//=== title ===
	NSRect titleBounds = NSMakeRect(marge+26, top+3, boxwidth-54, indexdiameter);
	
	//=== bottom ===
	NSBezierPath *pathBottom = [NSBezierPath bezierPath];
	// cut -> 5 bezier operations
	
	//=== drawing ===
	NSShadow *shadow = [[NSShadow alloc] init];
	[shadow setShadowOffset:NSMakeSize(0,-2)];
	[shadow setShadowBlurRadius:4];
	[shadow setShadowColor:[NSColor colorWithCalibratedWhite:0.5 alpha:0.8]];
	[NSGraphicsContext saveGraphicsState];
	[shadow set];
	
	[[NSColor lightGrayColor] set];
	[pathTop fill];
	[[NSColor whiteColor] set];
	[pathBottom fill];
	
	[NSGraphicsContext restoreGraphicsState];
	
	if (selected) {
		[pathTop setLineWidth:2.0];
		[pathBottom setLineWidth:2.0];
		[[NSColor keyboardFocusIndicatorColor] set];
	} else {
		[pathTop setLineWidth:1.0];
		[pathBottom setLineWidth:1.0];
		[[NSColor lightGrayColor] set];
	}

	[pathTop stroke];
	[pathBottom stroke];
	[[NSColor whiteColor] set];
	[pathCircleNumber fill];
	[self prepareAttributesWithColor:[NSColor lightGrayColor]];
	[self drawString: string centeredIn: circleNumberBounds];
	[self prepareAttributesWithColor:[NSColor whiteColor]];
	[self drawString: title centeredIn: titleBounds];
	
	[shadow release];
	
	//=== focus ===
	if ( shouldDrawFocusRing ) 
    {
        NSBezierPath *pathFocus = [NSBezierPath bezierPath];
		// cut -> 8 Bezier operations
		[pathFocus closePath];
		[pathFocus setLineWidth:2.0];
		[[NSColor selectedMenuItemColor] set];
		[pathFocus stroke];
    }
	
}



----

A good litmus test would be to see if the long delay comes back if the view changes. In other words, do the initial drag, do another drag to make sure it's not pausing any more, do something to make the view have to redraw itself, then do the drag again. If the delay comes back after the view changed, your drawing code is too heavy.

----

Your problem could be that you are locking focus on an NSImage while you are drawing into a view. You might want to draw the closeBoxImage in your     init method. --zootbobbalu


----

Just tried : the delay come only on the very first drag. My subviews redraw themselves when i resize the window so i did that, made a setNeedsDisplay: on the superview and did a drag again without any delay.

----

Zoot is correct - you should be creating your images elsewhere (like init or awakeFromNib) and using them in the redraw. This is way too heavy for the drawRect ...

----

Regardless of whether the drawRect code is good or not dataWithPDFInsideRect:[self bounds] takes a lot longer the first time you call it.  I have seen the behaviour myself and have not found a way round it.  If anyone can post code to get the contents of a view drawn inside an NSImage that will work even if the view is not and has never been on screen that is faster than dataWithPDFInsideRect: I'd be happy.  Robbie

----

If that is the case then why not just have the view draw into an image?

    

- (void)awakeFromNib {
    NSView *view = [window contentView];
    [view lockFocus];
    NSBitmapImageRep *bir =
        [[[NSBitmapImageRep alloc] initWithFocusedViewRect:[view bounds]] autorelease];
    [view unlockFocus];
    NSImage *image = [[[NSImage alloc] init] autorelease];
    [image addRepresentation:bir];
    image [[TIFFRepresentation] writeToFile:@"/tmp/tiff.tiff" atomically:YES];
}


--zootbobbalu

----

If only that worked.  This is getting a bit off topic but the code above does not work for me as I have views that have never been on screen.  I am loading the views from plugins and then want to get the contents as an NSImage.  If I do the above I get:

2005-04-25 19:27:15.121 FolderSync[4384] *** Assertion failure in -[NSView lockFocus], AppKit.subproj/NSView.m:2748
2005-04-25 19:27:15.122 FolderSync[4384] lockFocus sent to a view whose window is deferred and does not yet have a corresponding platform window

Note that the neither the window the view is in when it's loaded from the plugin and the window the view will end up in are deferred!  I think my best solution will be a tab control with the tabs hidden.  Then all the views will be in a real valid window.

Robbie

*You'd make an excellent Windows programmer with approaches like that. ;-) "Let's not figure it out, let's just beat it with a hammer 'til it fits." -- I realize that sounds more cynical than I intended. How about, "Stick with it and do it right - you'll be a better programmer for it."*

----

If your views have never been on screen then how are they receiving mouse events?

"They're not (yet).  Basically I have a preferences window that loads each pane from a plugin.  When the users selects a second pane I want to fade the current one out and the new one in.  So the new one has never been on screen.  I tried the tabs thing.  Didn't work.  I'm now thinking about child windows"

----

I just figured out that i don't redraw the buttons on each     drawRect:. I'm creating the buttons only on the first draw, then it's testing if the button exists and if it is, it only change its bounds :

    
	//=== closebox ===
	closeBoxBounds = NSMakeRect(boxwidth-13-marge+2, top+3+3, 13, 13);
	if (!closeBox)
	{
		closeBox = [[NSButton alloc] initWithFrame:closeBoxBounds];
		
		// �
		// and blah blah blah�
		// �

	} else [closeBox setFrame:closeBoxBounds];


So i don't think the slow down comes from here. -StephaneDassieu

----

Well since you're complaining of a slow first-drag, I'd say you're wrong. Create the images on init or on awakeFromNib and that's one less thing it has to do the first time around the drag, right? It can only help.

----

Maybe you can try like this:
    
	NSImage *im = [[NSImage alloc] initWithSize:
                NSMakeSize([view frame].size.width, [view frame].size.height)];
	[im lockFocus];
	[view drawRect:[view frame]];
	[im unlockFocus];
	
	//Do with "im" what you want 


----

Ok, i just did the last code above and it's MUCH faster. But it only draw a part of the drawing that's maybe why.. i have to dig into that but thanks for your help.
I didn't tried the example with the tempory tiff file in the awakeFromNib because i gave several instances of each bloc and i don't want to save a temp tiff file for each bloc.
I may try to create the image on     awakeFromNib: and keep it in memory.. but i don't know if it is  good thing.


*
I just had the same speed problem with     dataWithPDFInsideRect:, so I came up with this code. I believe the slowdown is in     dataWithPDFInsideRect:.
*
--AndreiKozlov

----

The temp tiff file was just a proof that you could get a valid drawing. Sorry for not making that clear. --zootbobbalu

