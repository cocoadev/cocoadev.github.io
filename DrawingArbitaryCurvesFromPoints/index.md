---
layout: page
title: DrawingArbitaryCurvesFromPoints
---



I'm writing a small graphical calculator, that draws curves/lines based on the equation given by the user. I've already written the parser, and it works fine (I wrote it in X-Code and then i had to port it to Windows for various reasons, but since it was written in C, it works fine there as well with minimal changes).

The problem I'm facing is, how can I draw a curve in NSView using various points given to me by the parser? I've looked at NSBezierPath, and it kept giving me very weird results... Is NSBezierPath the right to do this? Or am I on the wrong track entirely? As I understand this, a NSBezierPath can't really draw a perfect sine curve anyway?

In Windows, I simply draw the curve point by point (coloring each pixel using the coordinates given to me by the parser), I tried doing this in Cocoa using NSRect, but it gave me ugly results (the curve just doesn't look good, it IS correct though).

Thanks in advance.

----

NSBezierPath is probably the way to go. What's "weird" mean? To plot arbitrary functions, using very short straight line segments is probably the easiest way to do this. Coming up with the actual cubic equations needed to minimally fit a bezier path to an arbitrary function might be possible but my hunch is that the maths involved there is probably exceedingly tough. (There are simpler formulae for approximating sine curves, etc but if your parser can plot arbitrary functions they are only going to help you in a very tiny proportion of cases). Another option is to use short straight line segments and then curve fit the result to help smooth it and minimise it if needed (my own GCDrawKit uses a modified form of Philip Shneider's Graphics Gems code to do curve fitting with NSBezierPaths and the results are generally pretty good). I wouldn't bother trying to plot individual points using rects or other paths - just join each point to the previous one using a line. The resolution of the spacing between points can be made <1 pixel to ensure smoothness but of course more points takes longer to draw. --GrahamCox 

----

(Disclaimer: My numerical methods skill is a bit rusty -- people should feel free to chime in and correct anything I may have misstated here.)

Since NSBezierPath's curves are (from what I can tell) standard cubic Bezier splines, what I would do is split the user-supplied function to be plotted into n parts (where n is a parameter of your choosing, more below), then compute the control points for Bezier curves that approximate each part of the actual function. The implementation is pretty easy, it's just the math that is a little tricky.

Let's say you have a function f(x), and you're trying to compute the curve for a part of the function in the interval [a,b]. A Bezier cubic curve needs four control points to determine its shape, so what if we just evaluate four equally spaced points in the interval [a,b] and use those?  The problem with this is, not all of the control points for a Bezier curve actually lie on the curve.  The endpoints do, but the two middle points do not (unless the curve is a straight line).  So, given four points on the curve, the goal is to compute the control points that would generate a Bezier cubic spline through those four points.  (And we only need 4 points, because that's the number required to completely and uniquely determine a cubic curve, if I remember my interpolation theory correctly (and I may not!))

The parametric equation for a cubic B-spline is:  B(t) = (1 - t)^3 * P0 + 3t(1 - t)^2 * P1 + 3t^2 (1 - t) * P2 + t^3 * P3, where the P's are the control points, and t is in [0, 1].  Let's sample f(x) at four equally spaced points in the interval [a,b] and call those points Q0, Q1, Q2, and Q3. Since the endpoints of the Bezier curve should match the function f(x), we can easily determine those control points as P0 = Q0 and P3 = Q3.  Now all that is left is to solve for P1 and P2.

Since we're doing equally spaced points in the interval, Q1 is a third of the distance in, and Q2 is two-thirds.  So, Q1 = B(1/3) and Q2 = B(2/3). Expanding these, we get

Q1 = (8/27)P0 + (4/9)P1 + (2/9)P2 + (1/27)P3

Q2 = (1/27)P0 + (2/9)P1 + (4/9)P2 + (8/27)P3

Since the only unknowns in these equations are P1 and P2, what we have here is a linear system of two equations in two unknowns.  It has a unique solution: P1 = (1/6)(-5 * Q0 + 18 * Q1 - 9 * Q2 + 2 * Q3), P2 = (1/6)(2 * Q0 - 9 * Q1 + 18 * Q2 - 5 * Q3). Now we have the four control points that we can pass into the NSBezierPath methods!

Obviously, since we're doing an approximation with smooth cubic functions, if the f(x) that you're interpolating has discontinuities (holes, sharp points because of discontinuous derivatives, etc.), the Bezier approximation may not look right.  That's where the parameter n comes in above -- the more partitions that you use to split f(x), the closer an approximation you'll get, at the cost of speed.  But if you're just doing some screen-based plots, you can cap the value of n based on the resolution you're drawing at.  Since you split the function into n pieces and you use 4 values in each piece to compute the approximation, then choosing n = plot.width/4 ought to give you a nearly pixel perfect representation of the function (possibly less, since the endpoints of adjacent intervals will overlap).

Here's some code I wrote to test it, and it seems to work well.

    
	int i;
	int ncurves = /* the number of times to partition the function */;
	float start = /* starting point of the interval */;
        float end = /* end point of the interval */;
	
	float x;
	NSPoint q0, q1, q2, q3, p1, p2;
	float size = (end - start) / ncurves;

	NSBezierPath* path = [NSBezierPath bezierPath];
	[path setLineWidth:0];

	for(i = 0; i < ncurves; i++)
	{
		x = start + i * size;
		
		q0 = NSMakePoint(x, f(x));
		q1 = NSMakePoint(x + size / 3, f(x + size / 3));
		q2 = NSMakePoint(x + 2 * size / 3, f(x + 2 * size / 3));
		q3 = NSMakePoint(x + size, f(x + size));
		
		p1.x = (-5 * q0.x + 18 * q1.x - 9 * q2.x + 2 * q3.x) / 6;
		p1.y = (-5 * q0.y + 18 * q1.y - 9 * q2.y + 2 * q3.y) / 6;
		p2.x = (2 * q0.x - 9 * q1.x + 18 * q2.x - 5 * q3.x) / 6;
		p2.y = (2 * q0.y - 9 * q1.y + 18 * q2.y - 5 * q3.y) / 6;
		
		[path moveToPoint:q0];
		[path curveToPoint:q3 controlPoint1:p1 controlPoint2:p2];
	}

	[path stroke];


This is a very basic solution, though.  It could be smarter, by taking advantage of properties of the curve (such as where discontinuities occur, using adaptive interval sizes instead of a fixed size across the entire function, and so on), but if all you need to do is plot the curve on the screen, something like this might do the trick. --TonyAllevato

----

This is great, and I didn't even ask the original question :) one thing I'd mention though - after the first segment has been computed, don't do additional moveTo operations - just append the subsequent curves (unless there is a genuine discontinuity). While the extra moveTo's don't make any visual difference, they'll really interfere with a path editor, and they consume unnecessary rendering cycles. The other thing to say about a curve-fitting approach is that it generally works out n for itself so that the result is tested against the original points for closeness within some arbitrary tolerance value, basically starting with a small n and then increasing it as necessary until the curve fits to the specified tolerance. The advantage is that it minimises the number of bezier segments needed. It might be possible to come up with a hybrid of the two approaches (?) so that it would be effectively self-adapting to both the function to be plotted and the resolution of the display. --GC

----

Hi, 

I'm writing a simple plotting framework, mostly to plot data from text files. But it would be cool to add the ability to plot equations. I would love to have a look at your equation parser code. It's something that I've always thought was a bit too daunting to attempt!

Check out my progress on making a simple (and well documented) plotting framework using Cocoa:
http://www.boyfarrell.com/code/chocplot/doc/

I have been concentrating on getting a solid 'model' up and running once I'm there I will be adding the GUI classes.

Cheers.

-- Dan

----

Actually it's not as hard as it seems - use something like BISON and FLEX (both available in Xcode) to generate a parser from a formal description of the "grammar" of the parser you desire. The O'Reilly book on Flex and YACC (an earlier but compatible version of BISON) will tell you how to do this. Once you can parse the equation, generating plots from it is a case of iterating with a changing variable (or two).  I have C++ code for a parser built this way that handles all the maths operations available in the IEEE math spec and a few more besides, and allows arbitrary variables to be defined which makes plotting a breeze. It was part of my venerable MacZoop framework but as far as I recall isn't tied to that framework very strongly - it might be a good time to dust it off and make it available as a useful piece of code in its own right, now that MacZoop is no more. However, it's also fun to create your own just for the challenge ;-) --GrahamCox

----

Hi,

Last time I was looking into doing this somebody suggest BISON and FLEX as the way to go. Unfortunately I just don't have the bug for it. I'm much more interested in focusing on the drawing and model (algorithm) elements. I would be cool to plug into somebody else software as a library or even use some ready made code, such as the first posters C equation parser. What are you doing with that C++ parser code theses days? Is it sharable? Having said that I have never used C++ so I don't know how much sense it will make. What I 'm really after is a Cocoa(ish) or C equation parser (library or source-code).

-- Dan J F

----

I dug out my code and I was right - it's not tied to Maczoop in any significant way. So I made a nice Cocoa wrapper for it and I'll post it on my website tomorrow (it's a little bit too big to post here I think). Using it is simplicity itself - you can use the parser object or a simple category on NSString:

    

NSString* mathstring = @"3+4/5*6";
double value = [mathstring evaluateMath];   // will return 7.8000...01



This usage makes a new parser and throws it away each time, which means any variables you declare in an expression are discarded. So for graph plotting use, you'll probably want to keep the parser object hanging around. This is more efficient because the symbol table is only built once, and you can use the parser in a loop where one or more variables are changing - say when plotting graphs. For example:

    

GCMathParser* parser = [GCMathParser parser];
NSString* expression = @"sin(x)";
double x, y;

for( x = -pi; x <= pi; x += 0.01 )
{
    [parser setSymbolValue:x forKey:@"x"];
    y = [parser evaluate:expression];
    // do something with y here, e.g. plot it on a graph of x versus y
}



The code comes as a very simple one-window app that allows you to type expressions into a field, click a button and see the answer. Internally it uses code generated by BISON with a handcoded lexer - that part is pure C, and I #ifdef'd out the C++ stuff. Download from: http://apptree.net/parser.htm  --GC

----

Hi Graham,

Think it must be one of the only Objective-C equation parsers out there! I'll have a look when your server problems have passed, can you post a link?

Cheers,

- DJF

----

Code posted, link above. Enjoy ;-) --GC

----

Hi Graham,

Just downloaded a copy! I'll get stuck in later. By the way, It would be cool to make post over on MacResearch.org about CMMathParser. MacResearch.org is a community of scientific programmers, or more generally people that use macs in science. This they could get a lot of use out  or it. It that cool? 

-- DJF

----

Back to the original topic, I've had some luck using the technique listed in **An Algorithm for Automatically Fitting Digitized Curves ** published in Graphics Gems vol 1 http://www.graphicsgems.org/. That article provides a technique for approximating curves made up from discrete sources (pixels, points, what have you) with bezier paths. Its easily adaptable for use with NSBezierPath or CG drawing primitives. Source is available online here:

 * http://tog.acm.org/resources/GraphicsGems/gems/FitCurves.c
* http://tog.acm.org/resources/GraphicsGems/gems/GGVecLib.c
* http://tog.acm.org/resources/GraphicsGems/gems/GraphicsGems.h 

I like this solution in that it basically draws curves that touch specific points in space. That means it can easily draw curves for functions that are not continuously differentiable. You can easily draw circles, spirals, chaotic attractors, whatever you want. 

-- MikeTrent

----

This is the Schneider code I referred to above. I actually tried porting that exact code a while back as part of DrawKit, and found it to be a real mess. There is a much-cleaned up version of it available as part of InkScape which was in far better shape. The curve-fitting code I have in DrawKit now is possibly based on it (I actually forget now which implementation I went with) and DrawKit is available as a beta download and includes Cocoa categories on NSBezierPath that will perform the curve fitting. http://apptree.net/drawkitmain.htm   The quickest way to see it in action is to draw using the pencil tool in the demo - it accumulates samples of the mouse position as you drag and curve fits a bezier path to it in real time. --GC

----
If there's an award for best CocoaDev posting, it should go to TonyAllevato! His stuff is clear, concise, informative, well-written, and the code provided is immediately useful. Great work! -CS

----

Well, then, I'm happy to complete the citation then :) I didn't notice the reference my first time through. FWIW, I didn't have any trouble integrating the GraphicsGems code in my Cocoa app, though I did read through the errata. I have seen approaches that integrate this algorithm as a category on NSBezierPath; I find this awkward because 1) sometimes semantic usage doesn't exactly line up, and 2) sometimes I need to use CG primitives instead of NS. Both minor concerns. If someone else has done the work to modernize this code, by all means, use it. -- MikeTrent

----

I reminded myself - that is the code I used, though in C++ form which I believe was done by Lauris Kaplinski. The main improvement I found in this version was the shallow dependency tree making it much easier to drop into a Cocoa project without bringing a huge amount of unneeded baggage with it, and the much cleaner ANSI compliant coding which compiles without a whole load of warnings (I tend to work with quite strict warnings turned on). The downside of course is the need to deal with C++ which might put off some. 

Regarding NSBezierPath categories, that's just a convenience layer - I like NSBezierPath since it's a handy way to pass a very long list of NSPoints as a single object. An array of the same requires wrapping each in an NSValue which doesn't seem very efficient. Internally it all gets unpacked into a simple array of Geom::Points, passed to the     bezier_fit_cubic_r function then the result accumulated back into a new path. I also have logic that tries to make a good guess at where the input path turns a sharp corner and subdivides the path at that point. Doing something similar using CGPaths would amount to much the same, though a quick glance at the docs doesn't reveal how one can actually walk a CGPath and get at each individual path segment in order - using an applier function I suppose, which is how I have handled converting from a CGPath->NSBezierPath before. --GC

