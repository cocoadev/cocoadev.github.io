---
layout: page
title: MatrixInversionAlgorithm
---

I don't need an entire math package; something simple will do, to perform inversion on a 3x3 matrix. 

----

If you're just doing 3x3 matrices you might as well hardwire it yourself.

if 
    
    [ a11 a12 a13]
A = [ a21 a22 a23]
    [ a31 a32 a33]          


then     A^(-1) exists if     det(A) != 0 (or is within a small radius of 0 if you use floating point numbers), where

    
det(A) = a11*a22*a33+a12*a23*a31+a13*a21*a32 -a31*a22*a13-a32*a23*a11-a33*a21*a12


and

    
            1     [a22*a33-a23*a32  a13*a32-a12*a33  a12*a23-a13*a22]
A^(-1) = -------- [a23*a31-a21*a33  a11*a33-a13*a31  a13*a21-a11*a23] 
          det(A)  [a21*a32-a22*a31  a12*a31-a11*a32  a11*a22-a12*a21]


That translates pretty directly into code.
----

On the off chance that someone else might need to do a 3x3 matrix inversion, here's the code I ended up with:
    
- (IBAction)invertMatrix:(id)sender {
	NSArray *mIn = [NSArray arrayWithArray:[matrixIn cells]];
	
	int index, row, col;
	float a[3][3];
	float determinant;
	float aInverted[3][3];
	
	index = 0;
	for(row = 0; row < 3; row++) {
		for(col = 0; col < 3; col++) {
			a[row][col] = mIn objectAtIndex:index++] floatValue];
		}
	}
	
        determinant = a[0][0]*a[1][1]*a[2][2] + a[0][1]*a[1][2]*a[2][0] +
	              a[0][2]*a[1][0]*a[2][1] - a[2][0]*a[1][1]*a[0][2] - 
	              a[2][1]*a[1][2]*a[0][0] - a[2][2]*a[1][0]*a[0][1];
	
	aInverted[0][0] = (a[1][1]*a[2][2] - a[1][2]*a[2][1])/determinant;
	aInverted[0][1] = (a[0][2]*a[2][1] - a[0][1]*a[2][2])/determinant;
	aInverted[0][2] = (a[0][1]*a[1][2] - a[0][2]*a[1][1])/determinant;
	
	aInverted[1][0] = (a[1][2]*a[2][0] - a[1][0]*a[2][2])/determinant;
	aInverted[1][1] = (a[0][0]*a[2][2] - a[0][2]*a[2][0])/determinant;
	aInverted[1][2] = (a[0][2]*a[1][0] - a[0][0]*a[1][2])/determinant;
	
	aInverted[2][0] = (a[1][0]*a[2][1] - a[1][1]*a[2][0])/determinant;
	aInverted[2][1] = (a[0][1]*a[2][0] - a[0][0]*a[2][1])/determinant;
	aInverted[2][2] = (a[0][0]*a[1][1] - a[0][1]*a[1][0])/determinant;

	index = 0;
	for(row = 0; row < 3; row++) {
		for(col = 0; col < 3; col++) {
			[[mIn objectAtIndex:index++] setFloatValue:aInverted[row][col;
   // NSLog(@"Row %i, Col %i set to: %f",row,col,aInverted[row][col]);
		}
	}
}

This is set up to work with a GUI that has a 3x3 NSMatrix of text fields (matrixIn) and a button to initiate the inversion. Your .h file would therefore look something like:
    
#import <Cocoa/Cocoa.h>

@interface Inverter : NSObject
{
    IBOutlet NSMatrix *matrixIn;
}
- (IBAction)invertMatrix:(id)sender;
@end


The code writes the inverted matrix back out to the GUI. Clicking the "invert" button again will get you back to your original entries. Note, though, that it's using floating point (and, in particular, dividing by a floating point) so the usual accuracy issues are evident when you repeatedly click the button. This wasn't an issue for what I needed but you may need to use a more accurate method.

--PaulPomeroy

