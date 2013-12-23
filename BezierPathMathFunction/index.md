---
layout: page
title: BezierPathMathFunction
---

Can anyone tell me what are the mathematical algorithms used to draw Bezier paths ?
I want to locate any possible coordinates along the curve with a defined resolution...

-- Trax

----

*Can anyone tell me what are the mathematical algorithms used to draw Bezier paths?*

1. Line algorithms(for example the EFLA and the Bresenham algorithm)
2. Quadratic & cubic curve algorithms(for example the midpoint method and/aka the Casteljau's algorithm)
3. Line cap, line join, miter limit, line width and line dash style algorithms.

Arcs are actually converted into cubic curves.

I want to locate any possible coordinates along the curve with a defined resolution?

It looks like that you just need to flatten your curve/path. And change the flatness value in order to change the 'resolution'. (NSBezierPath has methods for both operations).

-- JP

----

Here are two of my C++ 2d bezier path functions, a recursive one and a quadratic one. 

Note that PointList is a templated list of points, but could easily be replaced with something like std::vector or std::list. And the point class was just your standard Point2D C++ class with overloaded operators. For reference, this came from an old 2d subpixel precise graphics API I worked on several years ago for BeOS... coming to OS X, Quartz obviated any use for this, for me.

    

#define BEZIER_LIMIT 6

/****************************************
	The Quadratic functions will add points into
	the PointList in order, from
	p to q. This is requisite if the resulting point list
	needs to be in order (as is the case for determining
	if a point lies within polygon geometry) but not
	the case for scanline-cache building since the final
	cache will be sorted in the process of its construction.
	Thus we can use the Recursive method, which, while
	it hits the stack harder, may provide a superior subdivision.

****************************************/

void g_buildBezierCurveQuadratic(PointList *list, Point2D p, 
	Point2D pa, Point2D qa, Point2D q)
{
	double maxSegments = 100; //an arbitrarily high number
	double dt = 1.0 / maxSegments;
	
	//build out our quadratic	
	double cx3 = -p.x + 3 * (pa.x - qa.x) + q.x;
	double cy3 = -p.y + 3 * (pa.y - qa.y) + q.y;
	double cx2 = 3 * (p.x - 2*pa.x + qa.x);
	double cy2 = 3 * (p.y - 2 * pa.y + qa.y);
	double cx1 = 3 * (pa.x - p.x);
	double cy1 = 3 * (pa.y - p.y);
	double cx0 = p.x;
	double cy0 = p.y;
	double t = 0;
	
	/*
		granularity is the distance between subdivided points inserted 
		(in raster coordinates) this number is presquared, so we don't 
		have to square-root the distance calculation below. so the distance 
		calculation is actually the sqrt of this
	*/
	bool drewFinalSegment = false;

	Point2D thisPoint;

	list->add(p);
	for (double i = 0; i < maxSegments; i++){
		t += dt;
		
		thisPoint.x = ((cx3 * t + cx2) * t + cx1) * t + cx0; 
		thisPoint.y = ((cy3 * t + cy2) * t + cy1) * t + cy0;
		
		/*
			don't enter until we've come up with a y-value more than n pixels 
			from the last point. (Distace2 returns the distance squared)
		*/
		if (list->ptrLast()->distance2(thisPoint) > BEZIER_LIMIT)
		{
					
			if (i >= (maxSegments - 1))
			{
				//connect to final point (q)
				thisPoint = q;
				drewFinalSegment = true;
			}

			list->addWithoutRedundancyCheck(thisPoint);
		}
		
	}	

	//if the final point wasn't drawn, put it in.
	if (!drewFinalSegment)
	{
		list->addWithoutRedundancyCheck(q);
	}
}

void g_buildBezierCurveQuadraticFast(PointList *list, Point2D p, 
	Point2D pa, Point2D qa, Point2D q)
{
	double maxSegments = 30; //an arbitrarily low number
	double dt = 1.0 / maxSegments;
	
	//build out our quadratic	
	double cx3 = -p.x + 3 * (pa.x - qa.x) + q.x;
	double cy3 = -p.y + 3 * (pa.y - qa.y) + q.y;
	double cx2 = 3 * (p.x - 2*pa.x + qa.x);
	double cy2 = 3 * (p.y - 2 * pa.y + qa.y);
	double cx1 = 3 * (pa.x - p.x);
	double cy1 = 3 * (pa.y - p.y);
	double cx0 = p.x;
	double cy0 = p.y;
	double t = 0;
	
	/*
		granularity is the distance between subdivided points inserted 
		(in raster coordinates) this number is presquared, so we don't 
		have to square-root the distance calculation below.
		so the distance calculation is actually the sqrt of this
	*/
	bool builtFinalSegment = false;

	Point2D thisPoint;

	list->add(p);
	for (double i = 0; i < maxSegments; i++){
		t += dt;
		
		thisPoint.x = ((cx3 * t + cx2) * t + cx1) * t + cx0;
		thisPoint.y = ((cy3 * t + cy2) * t + cy1) * t + cy0;

		if (thisPoint == q) builtFinalSegment = true;
		list->addWithoutRedundancyCheck(thisPoint);
	}	

	//if the final point wasn't drawn, put it in.
	if (!builtFinalSegment)
	{
		list->addWithoutRedundancyCheck(q);
	}
}


void g_buildBezierCurveRecursive(PointList *list, Point2D p, Point2D pa, Point2D qa, 
	Point2D q, double segLength2)
{
	if (p.distance2(q) < segLength2)
	{
		list->addWithoutRedundancyCheck(p);
	}
	else
	{
		Point2D a = p.middle(pa), b = q.middle(qa), c = pa.middle(qa);
		Point2D a1 = a.middle(c), b1 = b.middle(c);
		Point2D c1 = a1.middle(b1);
		g_buildBezierCurveRecursive(list, p, a, a1, c1, segLength2);
		g_buildBezierCurveRecursive(list, c1, b1, b, q, segLength2);	
	}
}



It should be easy enough to adapt the logic here to whatever language/api you need. The recursive is easiest to grok, and easiest to extend to 3d if you need.

Now, to answer the question of points along a curve, I have a couple functions I used to place and arbitrary point some distance from the beginning of a curve to the end. 

    

/*
	returns a point 'unitsFromAToB' along the line defined by a->b
	If the distance is greater than the line is long, the point is projected
	beyond b.
*/
Point2D g_pointOnLine(Point2D a, Point2D b, double unitsFromAToB)
{
	if (a == b) return a;

	//this *should* work for all lines a->b, given a != b
	double dy = b.y - a.y;
	double dx = b.x - a.x;
	double d = a.distance(b);
	
	double nx = unitsFromAToB * dx / d;
	double ny = unitsFromAToB * dy / d;
	
	Point2D p(a.x + nx, a.y + ny);
	return p;
}

/*
	Takes a curve generated by one of the bezier curve functions and makes a point
	'unitsFromStartToStop' along that curve, written into 'pols', writing the segment
	the intersection occurs on into 'lineSegIndex'

	If the curve is closed, e.g, a oval or square or circle, pass true for 'closed'.

	If the curve isn't closed, and 'unitsFromStartToStop' passes the endpoint, this
	function returns false. Otherwise, it returns true.
*/
bool g_pointOnLineSequence(PointList *sequence, double unitsFromStartToStop, Point2D *pols, 
	Sint32 *lineSegIndex, bool closed)
{
	//sanity check.
	if (unitsFromStartToStop < 0) return false;

	double progress = 0;
	Sint32 pCount = sequence->count();
	Sint32 startPointIndex = 0;

	for (startPointIndex = 0; startPointIndex < pCount; startPointIndex++)
	{
		//get the length of the current segment
		double segLength = sequence->ptrElementAt(startPointIndex)->
			distance(sequence->ptrElementAt(startPointIndex + 1));
		
		if ((segLength + progress)	< unitsFromStartToStop)
		{
			progress += segLength;
		}

		else // we've overshot (eg, found segment)
		{			
			/*
				if the shape isn't closed, check to see if
				point overshoots end of line sequence

				unitsFromStartToStop is longer than the 
				segment list
			*/
			if (!closed && (startPointIndex >= pCount - 1)) 
			{
				/*
					we're going to return false to notify 
					the caller that the point went beyond 
					the end of the line sequence, but we 
					should still write data into the params. 
					So we enter the final point in the 
					sequence into 'pols',  and the index of 
					that point into 'lineSegIndex'
				*/

				*pols = *sequence->ptrLast();
				*lineSegIndex = pCount - 1;
			
				return false;
			}
				
			break; //we're done
		}
	}
	
	//now generate a point on this line segment
	Point2D p = sequence->elementAt(startPointIndex);
	Point2D q = sequence->elementAt(startPointIndex + 1);
	
	*pols = g_pointOnLine(p, q, unitsFromStartToStop - progress);
	*lineSegIndex = startPointIndex;
	
	return true;
}



I used these functions (and many, many, many others) to handle stroking, corner mitering, path intersection and so on and so forth. They're primitive, but they're reasonably fast and I never had reliability troubles with them. If at all possible, you should stick with Quartz where possible, since Apple's engineers know more about this stuff than I do.

If you're interested in calculating the actual intersection points of arbitrary paths, I have some code that will do that too... I had a while back revived this API to explore writing an Out Of This World style 2d vector graphic game in SDL with my own raster code. It didn't make it very far ;)

--ShamylZakariya

