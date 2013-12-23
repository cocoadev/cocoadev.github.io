---
layout: page
title: AltivecOptimization
---

I have to do some altivec optimizations on a cocoa code for a G4. It's the first time I'm doing this, so I'm a bit lost.
All data in the source code is stored in an NSArray, and I suppose I must move it to vectors. Am I right? Is there any method or function which does this for me?
Do you know any tutorial, explanation of operations with vectors, or sample code besides those in apple's website?

----

Here's some code I used in a similar vein:

    
 // This example sets up an array that you can pass to a C function that
 // expects a 2D array argument
 // I use the LSFDDataPoint as a class to get the data into an NSTableView
 // My NSArray object contains a bunch of LSFDDataPoint objects
 // Maybe yours will just have NSNumber objects
 LSFDDataPoint *dataPoint;
 // Here's where your NSArray object comes in
 NSEnumerator *arrayEnumerator = [ dataArray objectEnumerator ];
        NSString *label;
 double data1, data2, data3, data4;
 int rowIndex = 0;
 int numRows = [ dataArray count ];
 	
 // The following lines create a file in which to dump data
        // and results for each run automatically
 // File will always show up in same directory as the executable bundle
 // NSString *bp = [ [ NSBundle mainBundle ] bundlePath ];
 // NSMutableString *fp = [ NSMutableString stringWithString: bp ];
 // [ fp appendString: @"/../FitData" ];
 // [ fp appendString: [ NSString stringWithFormat: @"%03d", ++fileCount ] ];
 // [ fp appendString: @".txt" ];	
 // FILE *lsfdf;
 // const char *fullPath = [ fp cString ];
 // lsfdf = fopen ( fullPath, "w" );
 	
 [ results setString: @"" ];
 
 while ( dataPoint = [ arrayEnumerator nextObject ] )
 {
 	rowIndex += 1;
 	
 	label = [ dataPoint pointLabel ];
 	
 	data1 = [ dataPoint data1 ];
 	c[ 0 ][ rowIndex ] = ( float ) data1;
 	data2 = [ dataPoint data2 ];
 	c[ 1 ][ rowIndex ] = ( float ) data2;
 	data3 = [ dataPoint data3 ];
 	
 	// Assuming that data3 is the vector of weights;
                // My program crashes if a data point weight is zero
 	if ( data3 == 0.0 )
 		data3 = 1.0;	// Point will not be weighted
 	
 	c[ 2 ][ rowIndex ] = ( float ) data3;
 	data4 = [ dataPoint data4 ];
 	c[ 3 ][ rowIndex ] = ( float ) data4;
 	// Automatically dump input data to file
 	// fprintf( lsfdf, "%s\t", [ label cString ] );
 	// fprintf( lsfdf, "%8.4f\t%8.4f\t%8.4f\t%8.4f\n", data1, data2, data3, data4 );
 	[ results appendString: label ];
 	[ results appendString:
 		[ NSString stringWithFormat:
                                @"%12.4f%12.4f%12.4f%12.4f\n", data1, data2, data3, data4 ] ];
 };


You get the idea...but...

You're on your own with managing those pesky vector optimizations...

----

So, c is the vector that you then use for your altivec-optimized code?

----

The optimization is beyond my expertise. Your question led me to believe you wanted
help moving your data from NSArray objects to standard C arrays, which is all the above code does.

In the above example c is a 2D array because each of my "data points" is itself a 'vector'.
I just assume that you will be using standard C arrays for AltiVec code.
But now you've gotten me interested in the subject. Sorry I couldn't be of more help here.

*no, you answered my question!*

----

I have had the need to write assorted code which uses altivec either directly (via the language extensions) or indirectly (via a library such as vDSP, see http://developer.apple.com/hardware/ve/).

In both cases, it has been my experience that if the data is properly aligned (which you get automatically if allocating on the heap with the gnu standard c library I believe) and in a good "order" (no need to stride through the vectors) it is pretty simple to use the dsp library that uses c arrays as arguments or roll your own altivec after a day of reading.

However, I have had a tough time applying altivec optimizations to unaligned data in some order that is difficult to use (my eyes get blurry after a certain number of vector permute operations).  Depending on the problem, it seems like the altivec speed boost gets offset by the pre and post data wrangling.

Just one programmer's observations.  My advice is, if at all possible, to keep your data in an altivec friendly format and encapsulate needed functionality it in a class that knows about the details.

----

When doing altivec stuff I use the vDSP library because its really easy but there is also the BLAST library and others. They require the the memory is aligned correctly I think 64 bit aligned but cannot be sure. Allocating with malloc does it correctly anyway. vector just make sure that when the memory is allocated on the stack it is correctly aligned.

