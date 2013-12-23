---
layout: page
title: ReturnFloatPointers
---



I have a float pointer and want to return it from class, is this the right way?

    
- (float *)amb
{
	float ret[4] = {x, y, z, w};
	return ret;
}


//Another file

float test[4] = [AClass amb];



----

It's the right way to return a pointer, but your code is because your array will be deallocated when amb returns (it goes out of scope). Try this:

    
- (float *)amb
{
	float* ret = calloc(sizeof(float),4);
        ret[0] = x;
        ret[1] = y;
        ret[2] = z;
        ret[3] = w;
	return ret;
}


//Another file

float* test = [AClass amb];


Don't forget to call     free(test) when you're done.

Alternatively, you could use an NSArray of NSNumber<nowiki/>s.

----

Another way would be to make the caller take care of memory allocation:
    
- (void)getAmb:(float *)array {
	array[0] = x;
	array[1] = y;
	array[2] = z;
	array[3] = w;
}

// call it like this:

float test[4];
[obj getAmb:test];


*this way would be more in keeping with Cocoa semantics*

----

Since this is obviously a 4-vector, I'd do it like this:
    
typedef struct
{
float x, y, z, w;
} V4;

-(V4)getAmb
{
V4 ret={x, y, z, w};
return ret;
}

Of course, that wouldn't be a pointer return.

----

You could even wrap that 4-vector up in an ObjC class if you wanted.

    
@interface fourVector : NSObject {
   float x, y, z, w;
}
- (id)initWithX:(float)_x y:(float)_y z:(float)_z w:(float)_w;
- (float)getX;
- (float)getY;
- (float)getZ;
- (float)getW;
@end


That way, you can take advantage not only of adding transformations, etc., to the interface, you can use ObjC's MemoryManagement to solve the return problem:

    
-(fourVector*)getAmb {
   return [[[fourVector alloc] initWithX:x y:y z:z w:w] autorelease];
}


Indeed, you could simply store a     fourVector in the class providing getAmb, instead of x,y,z and w. You could then give it a better name.

