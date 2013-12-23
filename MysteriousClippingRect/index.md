---
layout: page
title: MysteriousClippingRect
---

I've got a fairly complex visualization for state data dumped from my AI code; the visualization is drawn as a graph of nodes and lines and whatnot. The nodes have numeric labels, drawn via NSString's drawAtPoint message. Finally, the code that draws the graph sets up an NSAffineTransform to scale it, such that I can hit command plus/minus to zoom in and out. 

The graph itself scales fine, but the odd thing is that as I zoom out, the *text* begins to disappear, from right to left, bottom to top ( BTW, my coordinate system for the graph is flipped, with y=0 at the top ). If I zoom in ( make the graph larger ) there's no problem, and at 100% ( or scale=1.0 ) the text is fine.

If I drag and resize the window wider/taller while the scales is less than 1, I can see the text *appearing* on the right/bottom as the window gets bigger. By dragging the window very slightly, I can see the text being half-clipped, implying a clipping mask that resizes with the window, but isn't full view size.

My observation is this: Some kind of clipping rect is being respected by NSString's drawAtPoint method, but it is not being respected by NSBezierpath -- hence the graph draws fine, but the text is clipped. This clipping rect is not being scaled correctly, so when the affine transform is less than 1, the clipping rect is shrunken too.

What I'd like to know is how I can change this clipping rectangle, or how I can make my view disregard it completely. For what it's worth, I'm not concerned about performance in this situation.

Also, I tried overriding      -(BOOL) wantsDefaultClipping  to return NO and it still fails.

--ShamylZakariya

----
Maybe you could make the text into a NSBezierPath. The rendering in the end is however not as nice as the original text, and it is a little painful! I use that once to draw letters with different sizes from a very limited set (namely ACTG...)
    
NSFont *font=[NSFont fontWithName:fontName size:fontSize];
NSGlyph gly[5];
gly[0]=[font glyphWithName:@"H"];
gly[1]=[font glyphWithName:@"e"];
gly[2]=[font glyphWithName:@"l"];
gly[3]=[font glyphWithName:@"l"];
gly[4]=[font glyphWithName:@"o"];
NSBezierPath *thePath=[NSBezierPath bezierPath];
[thePath moveToPoint:NSMakePoint(0.0,0.0)];
[thePath appendBezierPathWithGlyphs:gly count:5 inFont:font];

There might be easier ways to get the glyphs that I don't know about.
Or why not make a category  of NSBezierPath with the new convenience method:

    - (void) appendBezierPathWithString:(NSString *)aString inFont:(NSFont *)fontObject];

----

Will this handle letter spacing correctly? I'm not drawing much text, really just labels for nodes on a graph.

--ShamylZakariya

----

In my case, I was actually using the Bezier paths for each individual letter from the set ACTG to display DNA binding site logos. The goal was to put the letters really the way i wanted it, with various heights and piled on top of each other, having them aligned, something like this:
http://www-lecb.ncifcrf.gov/~toms/icons/tata.gif

(this example is not from me! please visit the web site for more info)
I wanted a simple Cocoa app to do the same stuff. As I said, once I got the bezier path thing figured out, it was easy to do and worked quite well, except that the anti-aliasing does not work as well as what you get with real text.

--CharlesParnot

----

Last night I attempted to use the CG text functions to draw my text. I followed the example of Apple's example "Cocoa CG aliasing demo" [ http://developer.apple.com/samplecode/Cocoa_CG_aliasing_demo/Cocoa_CG_aliasing_demo.html ]

Now, the good news is it worked, and the odd clipping is gone. The bad news is, I'm baffled by the type size conversion. Where, in my original [NSString drawAtPoint] implementation I set my pointsize to 10, and it displayed correctly, looking about 10 points large on screen. However, when I set my point size to 10 in CG, I got letters about 150 pixels tall! HUGE. I had to drop my point size to about 1.1 to get them to display correctly.

The trouble is that this screams hack to me, particularly since I'm using the NSString sizeWithAttributes method to determine how to position my text so it fits cleanly in a circle or lozenge shape. If CG won't actually draw my text at the pointsize I want, I don't consider it to be an adequate solution.

Note that in the demo code, Apple set the pointsize to 4, and the letters look about 7o or 80 pixels tall. So it's not just me.

Is there some sort of known conversion factor, such as "multiply your intended point size by 0.11"? 

--ShamylZakariya

Does not that have something to do with the differences between points and pixels. I am not sure what the 'pointsize' is in Apple's example, and I am not a typist, so just a thought! --CharlesParnot

----

I don't mean to resurrect a dead, unsolved, topic, but I have to. The program for which I was developing the graphing view ended up being useless so I let it die. But I need the graphing view for a new program, and it's very important this time.

Anyway, I thought I'd get the ball rolling with an example image so people could see what I'm talking about. It's  PNG so it won't inline, since it's *tall*.

http://home.earthlink.net/~zakariya/files/MysteryClipRect.png [dead link]

[note: this is just testbed graphics rendering, not an actual graph!]

The image is four screenshots, at different zoom levels. At 100%, everything's peachy. At 50% you can see the text below a certain point dissapears, but oddly the NSBezierPath borders are still drawn. At 25% you can see that more has dissappeared, and when you resize the window, you can see the text being half-clipped in a manner following the window resize.

Since using CG text functions didn't pan out, can anybody tell me if there's some way to force clear any clipping rects in the Quartz rendering pipeline? I have to assume that the use of affine transforms is causing a faulty clip rect ( which is only being respected by the text rendering engine ).

This is important to me since I'm writing a visualization of behavioral networks my AI work is using. I need to be able to print the network ( which is very complicated ) so I can analyze it for errors. Since my printing code shrinks-to-fit this means labels will be lost.

--ShamylZakariya

----

You can try implementing -wantsDefaultClipping in your view and returning NO - this way you are responsible of setting your own clipping rect (according to the docs, I haven't tried it yet)

--SimoneTellini

----

You can introspect the clip (to see if that's really your problem) with CGContextGetClipBoundingBox().  Could you post more of your code?  drawAtPoint: doesn't clip at all, and clipping paths are never applied selectively with the single exception of focus rings.  Focus rings ignore the clip.

It seems more likely to me that there's a math error somewhere, possibly combined with unexpected behavior from drawAtPoint:.

----

Am I right in thinking that the REAL problem is that you want to zoom in/out your view? In which case, don't bother calculating your own transform, just let NSView do the work. You can call -(void) scaleUnitSquareToSize:(NSSize) size; and it will zoom the co-ordinate system for you. You also have to change the frame of the view to match the scale, and the clipping, etc is all handled. If you have rulers, they also just work and scale to whatever you set. The beauty of this is that you can draw your content completely oblivious to what's going on in the view - you could even set things up to have more than one view displaying your data, with different scales set in each.

Below I've posted a fragment of code that I use in my views. It's not self-contained, but you should be able to figure it out. Basically you need to add a data member called _scale which tracks the zoom scale, where 1.0 = 100%. This method accepts a relative zoom factor, not an absolute scale, but you could adapt it. I also include the bits of my code that link it to the scroll wheel, which is a nice feature - option-scrollwheel zooms in/out the view.

Update: I decided to bundle this code into a class that IS self-contained and does the zooming thing for you. You can download it here: http://apptree.net/gczoomview.htm you can simply subclass this instead of NSView and it adds all the zooming stuff you need. I realise it doesn't really address the question being asked here, but it might make the question go away altogether, which is often my approach when faced with something difficult to solve! --GrahamCox

    
- (void)				zoomViewByFactor: (float) factor andCentrePoint:(NSPoint) p
{
	// all zooms bottleneck through here. The scaling factor of the view is calculated from <factor> and the view coordinates
	// adjusted accordingly. This will also adjust any enclosing scroller, rulers, etc. The point <p> is centred in the view
	// by scrolling if necessary, and possible.
	
	if ( factor != 1.0 )
	{
		NSSize  newSize;
		NSRect  fr;
		float   sc;
		
		sc = factor * [self scale];
		
		if ( sc < [self minimumScale])
		{
			sc = [self minimumScale];
			factor = sc / [self scale];
		}
		
		if ( sc > [self maximumScale])
		{
			sc = [self maximumScale];
			factor = sc / [self scale];
		}
		
		if ( sc != [self scale])
		{
			_scale = sc;
			fr = [self frame];
			
			newSize.width = newSize.height = factor;
			
			[self scaleUnitSquareToSize:newSize];  //<------ NSView method does 99% of the work
			
			fr.size.width *= factor;
			fr.size.height *= factor;
			[self setFrame:fr]; // <------ This does the rest!!

			[self scrollPointToCentre:p];
			[self setNeedsDisplay:YES];
		}
	}
}


- (void)				zoomWithScrollWheelDelta:(float) delta toCentrePoint:(NSPoint) cp
{
	// zooms the view when the user adjusts the scroll wheel (with option key down)
	
	float factor = ( delta > 0 )? 0.9 : 1.1;
	
	[self zoomViewByFactor:factor andCentrePoint:cp ];
}


- (void)				scrollWheel:(NSEvent *)theEvent
{
	if (([theEvent modifierFlags] & NSAlternateKeyMask) != 0 )
	{   
		// note to self - using the current mouse position here makes zooming really difficult, contrary
		// to what you might think. It's more intuitive if the centre point remains constant
		
		NSPoint p = [self centredPointInDocView];
		[self zoomWithScrollWheelDelta:[theEvent deltaY] toCentrePoint:p];
	}
	else
		[super scrollWheel: theEvent];
}




----

I've encountered this clipping bug when implementing a view to render for the printer. 

My view's -drawRect: works like this

 - compute scale factor to fit a ~860 pixel wide page to the paper size
 - call CGContextScaleCTM()
 - invoke the on-screen rendering code
 - restore the previous scale

As a result, text is clipped starting from the right of the page (only on OS X 10.4 it works fine).
I've already tried to return NO from -wantsDefaultClipping and setting up a scaled clip rect with
an NSBezierPath and -setClip, but -drawAtString:withAttributes: still stops working after a certain X position.

I've implemented -knowsPageRange: and -rectForPage:, if it matters.

Should I try calling setFrame as well? (in -drawRect:?)

