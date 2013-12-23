---
layout: page
title: NSBezierPathcombinatorics
---



NSBezierPath is cool and all, but one key set of features are lacking, compared to, say, regions, in QuickDraw. For CLOSED shapes, it would be great to be able to quickly form the union, intersection, difference and XOR of any two paths, forming a new path. This would be incredibly powerful for graphics apps. You might suggest simply painting one path on top of another but that only works for solid fills. If you want to outline the result you're stuck. Putting multiple closed paths inside a single NSBezierpath works for some simple shapes, but not in the general case.

If anyone has code (or wants to work on this with me) and release it into the public domain, please speak up!

----
This open source package looks pretty promising: 

https://bitbucket.org/andyfinnell/vectorboolean

It contains a NSBezierPath category which implements the basic operations. Licensed under a MIT/BSD (I think) kind of license. Everything tied together into a neat example application.

Update: now that I have tried it, it turns out to not support the closepath command. That command is used with many paths that NSBezierPath create via the autorelased class methods, like +bezierPathWithRect:. At that point I didn't further look into it.

----

----
See Clipper - http://www.angusj.com/delphi/clipper.php - a polygon clipping library I've written in Delphi, C++ and C# and released under the Boost Software License.
----

----
See the opensource LibArt library.  Libart supports a full range of geometric operations on vector paths, including union, intersection (clipping), difference, symmetric difference ...   http://www.levien.com/libart/
----

Vector paths is relatively easy - there's lots of code out there, none better in my view than gpc [http://www.cs.man.ac.uk/~toby/alan/software/] which I have easily adapted to work with NSBezierPath. The difficulty arises when the relevant original bezier control points need to be retained so that the shapes resulting remain editable in their original form. Converting to vector paths loses these original control points and also leads to either a vast number of points for smooth curves, or obvious straight line segments for more manageable ones. I tried also using a curve-fitting approach to convert the vector path back to a bezier, but initial results weren't very encouraging. --GrahamCox

----

I need to solve this problem. I do think it's something that should be standard in NSBezierPath, (and I've added a vote on this over at AppKitMostWanted), but I'm not holding my breath and any way I need it for my apps now, not in OS X 10.6 or whatever. So to get started on this, I'll outline what I think is the way to solve this here. Feel free to jump in and contribute or discuss. Thanks! --GrahamCox

The goal is:

To form the union, intersection and difference of any two closed NSBezierPath objects. As a first pass, a simplification can be that the paths need only have single subpaths - no holes or disjoint regions. Ultimately this needs to be generalised to the more complex case of multiple subpaths.

Speed only needs to be "adequate". It is envisaged that this will be an operation that the user will apply to graphical entities manipulated on screen, so will be a relatively infrequent and manual operation.

Accuracy only needs to be "adequate". Since this is most likely to be used for graphical entities on screen, only visually correct intersections points and so forth are needed. 

The result of the operation is a new path which can be rendered as a single entity. As long as the path is correctly formed it will integrate with the existing graphics system directly.

The API would look something like:

    

@interface NSBezierPath (BooleanOps)

- (NSBezierPath*)   pathByFormingUnionWithPath:(NSBezierPath*) otherPath;
- (NSBezierPath*)   pathByFormingDifferenceWithPath:(NSBezierPath*) otherPath;
- (NSBezierPath*)   pathByFormingIntersectionWithPath:(NSBezierPath*) otherPath;




The algorithm is:

1. Test the paths in question for suitability for the operation. Non-closed paths or multiple subpaths can be rejected.

2. Find the points where each path intersects the other. For straight-line segments, this is fairly easy. For arbitrary bezier curve segments, there is no general solution to the equation for two curves, but it's easy enough to reduce each curve to a series of straight-line segments and find the intersections of the lines.Even a brute-force search will probably do here as it's unlikely that real paths will have more than a few tens of elements, resulting a few hundreds of line segments.

The intersection points will become new path points in the result.

3. Cut the paths at the intersection points. This is easy for straight-line segments and easy for bezier segments if the 't' value at the intersection point is known. Finding the 't' value for an arbitrary point isn't so easy, but can be found by a brute-force search. This can be the same search as the one used to find the intersections, so for efficiency it is supposed that we can combine these operations into one pass.

4. The next job is to discard all the path points that we don't want. Which points we discard depend on the operation:

a) for a union, we discard all the points on the second path that fall inside the first path, and all the points on the first path that fall inside the second. Paths need to be ordered so they are both in the same direction (clockwise, say)

b) for a difference, we discard all the points on the second path that fall outside the first path, and all the points on the first path that fall inside the second path. This cuts out the second path from the first.One path needs to be in the reverse direction to the other.

c) for an intersection, we discard all points except those that fall inside the other path. This is the inverse of the union case. Both paths need to be in the same direction.

The order of the points must be maintained, so a practical method might be to first mark all of the unwanted points. Then the first path is walked, and each marked point is ignored. When an intersection point is reached, we switch to the other path and keep walking. We need to decide at each intersection whether to "turn left or right". It's a simple operation to determine which side of the line a point lies. The direction of each path needs to be set up so that the walking round proceeds in the correct manner. I'm still a bit vague on this actually, as is probably obvious. NSBezierPath has a method to reverse a path, so it's easy to set up the paths in the desired direction to facilitate this.

Notes:

My view is that the points will all need to be extracted into a more accessible data structure for doing all of this rather than working in the NSBezierPath data structure directly.

The result of the operation needs to have as few control points as necessary to describe the result. Therefore converting to a standard vector path (SVP), doing the operation in that domain and then converting back is not a suitable option, as the result will massively multiply the number of control points. However, using an intermediate SVP representation to find many of the necessary points is acceptable, and probably inevitable.

---- more to come as this proceeds ----

----
If GPL is acceptable, there may be (I have it but I don't know if I can distribute it) a category on NSBezierPath that converts the bezier paths to a libart sorted vector paths, performs the union or intersection operations, and then converts back to NSBezierPaths composed solely of short vectors.

----
GPL is fine for me. The key point is converting back to bezier form. I haven't had much luck so far making this work reliably, but if someone else has, I'd certainly be willing to look at it. It may be that despite looking easy, handling this problem directly in the bezier "domain" might be impossible. --GC

----
Java's Area class provides this functionality, and it is emulated pretty well by GNU's classpath, of which the source is freely available. It has comments about it not being the most efficient algorithm, but it's general, so it works for complex paths as well. You could probably adapt the algorithm used there quite easily (obviously giving credit) but as was previously said, you'll probably need an intermediate data structure. --DanielPeebles

----
A quick review of http://java.sun.com/j2se/1.3/docs/api/java/awt/geom/Area.html indicates that Java's Area class reduces "areas" to closed shapes bounded by  "flattened outline... Only uncurved path segments represented by the SEG_MOVETO, SEG_LINETO, and SEG_CLOSE point types"  A quick survey of the Graphic Gems series and other academic literature fails to find any general solution to this problem.  

I suspect that whoever comes up with "a general solution for intersecting, unioning, and differencing closed paths composed of multiple Bezier curves while presenting the results in the form of more Bezier curves" will have advanced the state of the art in the industry.  I thought Adobe Illustrator had this feature, but maybe I am mistaken.

----

It would probably be in vain, but we could try and talk the OmniGroup to maybe opening up some their implementation from Graffle.

----
A less quick review of http://java.sun.com/j2se/1.3/docs/api/java/awt/geom/Area.html would indicate nothing of the sort :) The part you quote is the overloaded getPathIterator method that accepts a flatness parameter. It returns a flattened path iterator in case you need to perform the (rather common) operation of simplifying the Area. It is provided for convenience, and the normal getPathIterator is still available and will return the pure unmodified Area. The various CAG (constructive area geometry) operations do not simplify the Area before applying them, and will return (almost) arbitrarily complicated Areas depending on what you pass to them. At least, that's what the original Java implementation does, and since Classpath is designed as a drop-in replacement for the original Java classes, I'd assume it has the same behavior. --DanielPeebles
----
Well, this is great!  Java is open source now, so all we have to do is download it to see how it works.

----

Below I'm posting the code I have written to adapt the GPC (general polygon clipping) code to NSBezierPath. This partially solves the problem given here - it converts NSBezierPaths to vector paths, performs the union, intersection, difference, etc using the GPC library, then converts back to the NSBezierPath. It's very simple to use as it's a category on NSBezierPath. You also need to download Alan Murta's gpc library from http://www.cs.man.ac.uk/~toby/alan/software/ It's plain C and compiles for OS X without any modification. Note licensing terms. What this code does NOT do: the result consists of short vector paths (i.e. many, many LINETO segments) and does not preserve the original control points of the CURVETO segments in the input paths. This is the bit I'm now trying to solve, using either curve fitting, keeping the original paths around, or by using actual mathematics ;-) Still, this code is very useful even without this - GPC is a very fine piece of work, and much cleaner than other implementations I've looked at. Note that all this code does is to convert an arbitrary NSBezierPath to a gpc_polygon data structure, pass it to the gpc lib code, then convert it back afterwards. It does deal with paths containing holes, subpaths - gpc calls these "contours". Reasonably well-tested, I haven't found a shape it can't process yet. --GrahamCox.

// header file:

    

#import <Cocoa/Cocoa.h>
#import "gpc.h"


@interface NSBezierPath (GPC)

// utility methods:

+ (NSBezierPath*)		bezierPathWithGPCPolygon:(gpc_polygon*) poly;

- (gpc_polygon*)		gpcPolygon;
- (gpc_polygon*)		gpcPolygonWithFlatness:(float) flatness;

- (int)				countSubPaths;
- (int)				subPathCountStartingAtElement:(int) se;

- (BOOL)				intersectsPath:(NSBezierPath*) path;
- (NSBezierPath*)		pathFromPath:(NSBezierPath*) otherPath usingBooleanOperation:(gpc_op) op;

// boolean ops on bezier paths yay!

- (NSBezierPath*)		pathFromUnionWithPath:(NSBezierPath*) otherPath;
- (NSBezierPath*)		pathFromIntersectionWithPath:(NSBezierPath*) otherPath;
- (NSBezierPath*)		pathFromDifferenceWithPath:(NSBezierPath*) otherPath;
- (NSBezierPath*)		pathFromExclusiveOrWithPath:(NSBezierPath*) otherPath;


@end



And the implementation:

    

#import "NSBezierPath+GPC.h"

@implementation NSBezierPath (GPC)


+ (NSBezierPath*)		bezierPathWithGPCPolygon:(gpc_polygon*) poly
{
	// returns a new NSBezierPath object equivalent to the polygon passed to it. The caller is responsible for freeing
	// the polygon. The returned path is autoreleased as per usual cocoa rules.
	
	NSBezierPath*	path = [NSBezierPath bezierPath];
	NSPoint			p;
	int				cont;
	
	for( cont = 0; cont < poly->num_contours; ++cont )
	{
		p.x = poly->contour[cont].vertex[0].x;
		p.y = poly->contour[cont].vertex[0].y;
		[path moveToPoint:p];
		
		int vert;
		
		for( vert = 1; vert < poly->contour[cont].num_vertices; ++vert )
		{
			p.x = poly->contour[cont].vertex[vert].x;
			p.y = poly->contour[cont].vertex[vert].y;
			[path lineToPoint:p];
		}
		
		[path closePath];
	}
	
	// set the default winding rule to be the one most useful for shapes
	// with holes.
	
	[path setWindingRule:NSEvenOddWindingRule];
	
	return path;
}



- (gpc_polygon*)		gpcPolygon
{
	return [self gpcPolygonWithFlatness:0.1];
}


- (gpc_polygon*)		gpcPolygonWithFlatness:(float) flatness
{
	// returns a poly structure representing a flattened version of the receiver. The caller is responsible for
	// freeing the result structure using gpc_free_polygon() if the result is not NULL.
	
	[self setFlatness:flatness];
	
	NSBezierPath*			flat = [self bezierPathByFlatteningPath];
	NSBezierPathElement		elem;
	NSPoint					ap[3];
	int						i, ec = [flat elementCount];
	gpc_polygon*			poly;
	
	[flat setWindingRule:[self windingRule]];
	
	// allocate memory for the poly.
	
	poly = malloc( sizeof( gpc_polygon ));
	poly->contour = NULL;
	poly->hole = NULL;
	
	// how many contours do we need?
	
	poly->num_contours = [flat countSubPaths];
	poly->contour = malloc( sizeof( gpc_vertex_list ) * poly->num_contours );
	
	// how many elements in each contour?
	
	int es = 0;
	
	for( i = 0; i < poly->num_contours; ++i )
	{
		int spc = [flat subPathCountStartingAtElement:es];
	
		// allocate enough memory to hold this many points
				
		poly->contour[i].num_vertices = spc;
		poly->contour[i].vertex = malloc( sizeof( gpc_vertex ) * spc );
		
		es += spc;
	}
	
	// es will now keep track of which contour we are adding to; k is the element index within it.
	
	int k = 0;
	es = -1;
	NSPoint	 spStart;
	
	for( i = 0; i < ec; ++i )
	{
		elem = [flat elementAtIndex:i associatedPoints:ap];
		
		switch( elem )
		{
			case NSMoveToBezierPathElement:
			// begins a new contour.
			
			if ( es != -1 )
			{
				// close the previous contour by adding a vertex with the subpath start
				
				poly->contour[es].vertex[k].x = spStart.x;
				poly->contour[es].vertex[k].y = spStart.y;
			}
			// next contour:
			++es;
			k = 0;
			// keep a note of the start of the subpath so we can close it
			spStart = ap[0];
			
			// sanity check es - must not exceed contour count - 1
			
			if ( es >= poly->num_contours )
			{
				NSLog(@"discrepancy in contour count versus number of subpaths encountered - bailing");
				
				gpc_free_polygon( poly );
				return NULL;
			}
			
			// fall through to record the vertex for the moveto
			
			case NSLineToBezierPathElement:
			// add a vertex to the list
			poly->contour[es].vertex[k].x = ap[0].x;
			poly->contour[es].vertex[k].y = ap[0].y;
			++k;
			break;
			
			case NSCurveToBezierPathElement:
				// should never happen - we have already converted the path to a flat version. Bail.
				NSLog(@"got a curveto unexpectedly - bailing");
				gpc_free_polygon( poly );
				return NULL;
				
			case NSClosePathBezierPathElement:
			// ignore
			break;
		}
	}
	
	//logPoly( poly );
	
	return poly;
}


- (int)					countSubPaths
{
	// returns the number of subpaths in the object. This simply counts the number of moveTos
	
	NSBezierPathElement	et;
	int					sp, i, ec = [self elementCount];
	
	sp = 0;
	
	for( i = 0; i < ec; ++i )
	{
		et = [self elementAtIndex:i];
		
		if ( et == NSMoveToBezierPathElement )
			++sp;
	}
	
	return sp;
}


- (int)					subPathCountStartingAtElement:(int) se
{
	// returns the number of elements in the subpath starting at element <se>. The caller is responsible for setting se
	// correctly - it should be the index of a 'moveto' element. This counts up until the next moveTo or the end of
	// the path, and returns the element count.
	
	NSBezierPathElement	et;
	int					sp, i, ec = [self elementCount];
	
	sp = 1;
	
	for( i = se + 1; i < ec; ++i )
	{
		et = [self elementAtIndex:i];
		
		if ( et == NSMoveToBezierPathElement )
			break;
			
		++sp;
	}
	
	return sp;
}


- (BOOL)				intersectsPath:(NSBezierPath*) path
{
	// returns YES if the receiver's path intersects <path>. Note that this is not a trivial
	// operation, so try to avoid calling it willy-nilly.
	
	NSRect		bbox = [path bounds];
	
	if ( NSIntersectsRect( bbox, [self bounds]))
	{
		// bounds intersect, so it's a possibility - find the intersection and see if it's empty.
	
		NSBezierPath* ip = [self pathFromIntersectionWithPath:path];
		
		return ![ip isEmpty];
	}
	else
		return NO;
}


- (NSBezierPath*)		pathFromPath:(NSBezierPath*) otherPath usingBooleanOperation:(gpc_op) op
{
	NSBezierPath*	result;
	gpc_polygon		*a, *b, *c;
	
	a = [self gpcPolygon];
	b = [otherPath gpcPolygon];
	c = malloc( sizeof( gpc_polygon ));
	
      // this line does all the really hard work:

	gpc_polygon_clip( op, a, b, c );
	
	//logPoly( c );
	
	result = [NSBezierPath bezierPathWithGPCPolygon:c];
	
	//NSLog(@"path = %@", result );
	
	gpc_free_polygon( a );
	gpc_free_polygon( b );
	gpc_free_polygon( c );
	
	return result;
}


// high-level methods - all you need to call.

- (NSBezierPath*)		pathFromUnionWithPath:(NSBezierPath*) otherPath
{
	return [self pathFromPath:otherPath usingBooleanOperation:GPC_UNION];
}


- (NSBezierPath*)		pathFromIntersectionWithPath:(NSBezierPath*) otherPath
{
	return [self pathFromPath:otherPath usingBooleanOperation:GPC_INT];
}



- (NSBezierPath*)		pathFromDifferenceWithPath:(NSBezierPath*) otherPath
{
	return [self pathFromPath:otherPath usingBooleanOperation:GPC_DIFF];
}



- (NSBezierPath*)		pathFromExclusiveOrWithPath:(NSBezierPath*) otherPath
{
	return [self pathFromPath:otherPath usingBooleanOperation:GPC_XOR];
}


@end


// dumps poly structure to log for testing/debug

static void		logPoly( gpc_polygon* poly )
{
	// dumps the contents of the poly to the log

	NSLog( @"gpc_polygon: %p", poly );
	NSLog( @"contours: %d\n", poly->num_contours );
	
	int cont;
	
	for( cont = 0; cont < poly->num_contours; ++cont )
	{
		NSLog( @"contour #%d: %d vertices", cont, poly->contour[cont].num_vertices );
		
		int vert;
		
		for( vert = 0; vert < poly->contour[cont].num_vertices; ++vert )
			flog4Debug( @"{ %f, %f },", poly->contour[cont].vertex[vert].x, poly->contour[cont].vertex[vert].y );
			
		NSLog( @"------ end of contour %d ------", cont );
	}
	NSLog( @"------ end of polygon ------" );
}




