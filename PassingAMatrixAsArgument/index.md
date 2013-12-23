---
layout: page
title: PassingAMatrixAsArgument
---



I am trying to pass matrix from one object to another (of the same class). The error and warning i recieve is placed as comments right after the line that causes it. Can anyone help me out on this? I have no idea what goes wrong. Isn't it possible to just pass the matrix as any other variable?

*Sure, but you can't pass a matrix around in a BOOL variable, which is what you're trying to do.*

**You probably want acceptInput:width:height: to take a BOOL * instead.** *Or since it's a matrix, a     BOOL ***

    
// Output data to a given cell
-(void)outputTo:(Cell*)target
{
	BOOL outputBuffer[outputArea.w][outputArea.h];
	int x, y;
	
	for (x = 0; x <= outputArea.w; x++) {
		for (y = 0; y <= outputArea.h; y++) {
			
			outputBuffer[x][y] = matrix[outputArea.x + x][outputArea.y + y];
			
		}
	}
	
	
	[target acceptInput:(BOOL)outputBuffer width:outputArea.w height:outputArea.h];
	// Warning: "Cast from pointer to integer of different size"
	
}

// Accept input from another cell and inject data into matrix
-(void)acceptInput:(BOOL)inputBuffer width:(int)width height:(int)height
{
	int x, y;
	
	for (x = 0; x <= inputArea.w; x++) {
		for (y = 0; y <= inputArea.h; y++) {
			
			matrix[inputArea.x + x][inputArea.y + y] = inputBuffer[x][y];
			// Error: "subscripted value is neither array nor pointer"
		}
	}
	
}

