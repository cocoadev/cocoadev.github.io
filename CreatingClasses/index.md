---
layout: page
title: CreatingClasses
---

This is a header file from visual c language. How do i turn the class into an objective c/cocoa class?

    
#define PI 3.1415926535897932

#define BEHIND		0
#define INTERSECTS	1
#define FRONT		2


struct CVector3
{
public:
	

	CVector3() {}

	CVector3(float X, float Y, float Z) 
	{ 
		x = X; y = Y; z = Z;
	}

	CVector3 operator+(CVector3 vVector)
	{
		return CVector3(vVector.x + x, vVector.y + y, vVector.z + z);
	}

	CVector3 operator-(CVector3 vVector)
	{
		return CVector3(x - vVector.x, y - vVector.y, z - vVector.z);
	}
	
	CVector3 operator*(float num)
	{
		return CVector3(x * num, y * num, z * num);
	}

	CVector3 operator/(float num)
	{
		return CVector3(x / num, y / num, z / num);
	}

	float x, y, z;						
};


----

You don't get operator overloading in ObjC, so you'll have to implement the operators as methods with appropriate names; for instance     -byAdding:,     -byMultiplying:, and the like.

Now you need to look around the site here and see about how to make an ObjC class. There's lots of class definitions throughout this site; try searching for '@interface' (without the quotes) to find some.

-- RobRix

----

    
#define PI 3.1415926535897932

#define BEHIND		0
#define INTERSECTS	1
#define FRONT		2

@interface CVector : NSObject
{
	float x, y, z;
}

+ (id)vectorWithX:(float)xx Y:(float)yy Z:(float)z;
- (id)initWithX:(float)xx  Y:(float)yy Z:(float)zz;
- (id)add:(CVector *)vVector;
- (id)subtract:(CVector *)vVector;
- (id)multiply:(float)num;
- (id)divide:(float)num;
- (float)x;
- (float)y;
- (float)z;

@end

@implementation CVector

+ (id)vectorWithX:(float)xx Y:(float)yy Z:(float)zz
{
	return [self alloc] initWithX:xx Y:yy Z:zz] autorelease];
}

- (id)initWithX:(float)xx  Y:(float)yy Z:(float)zz
{ 
	x = xx;
	y = yy;
	z = zz;
}

- (id)add:([[CVector *)vVector
{
	return [CVector vectorWithX:([vVector x] + x) Y:([vVector y] + y) Z:([vVector z] + z)];
}

- (id)subtract:(CVector *)vVector
{
	return [CVector vectorWithX:(x - [vVector x]) Y:(y - [vVector y]) Z:(z - [vVector z])];
}

- (id)multiply:(float)num
{
	return [CVector vectorWithX:(x * num) Y:(y * num) Z:(z * num)];
}

- (id)divide:(float)num
{
	return [CVector vectorWithX:(x / num) Y:(y / num) Z:(z / num)];
}

- (float)x
{
	return x;
}

- (float)y
{
	return y;
}

- (float)z
{
	return z;
}


@end


----

Thanks - I will try it out later

----

The problem now is this:

this is the code:
    
#import "CVector.h"

CVector *vTemp;
int result = fscanf(levelfile, "%f %f %f\n", &vTemp.x, &vTemp.y, &vTemp.z);
%
i get error:

error: request for member 'x' in something not a structure or union

what is wrong?

*What is wrong is that vTemp is a pointer. You do not access members of pointed structs with the dot operator, but with the -> operator.*

btw the code compiled:)

----

Well, you made the C++ class into an Objective-C class but you are still trying to use it as a C++ class.  You should probably just stick to the C++ implementation unless you actually need some of the additional power/convenience of Objective-C.

----

If you made the x, y and z in CVector public (put "@public" on the line before  their declaration), then you could access them as CVectory->x.

*Note: true, but evil and also doesn't fix the code completely.  See below.*

----

Thanks it worked:)

...Yeah, but that is evil!!!...

----

Actually, the correction to your code fragment does fully correct the problem, in addition to being evil.  Memory is never allocated for the CVector; you're writing on some random chunk of memory.

To be more specific about the evilness:  variables are basically *never* directly accessed from outside a class in ObjC (it's allowed, but I've never seen someone do it until I saw it here). 

Here's a correct fragment.

    
float x,y,z;
int result = fscanf(levelfile, "%f %f %f\n", &x, &y, &z);
CVector *vTemp = [CVector vectorWithX:x Y:y Z:z];

----
Instead of trying to convert your code from C++ to ObjC, try actually just learning ObjC. It's obvious you don't know what you're doing in ObjC. Carrying over all of your preconceptions from C++ will *not* help you learn. Ditch the C++, start a new project from scratch, and keep in mind that ObjC and C++ are totally different languages with totally different approaches to the whole "Object Oriented" thing.

----

One last question

    
CVector vVector1 = vPolygon[2] - vPolygon[0];

//CVector3 operator-(CVector3 vVector)
//{
	//return CVector3(x - vVector.x, y - vVector.y, z - vVector.z);//C++
//}

- (id)subtract:(CVector *)vVector
{
        return [CVector vectorWithX:(x - [vVector x]) Y:(y - [vVector y]) Z:(z - [vVector z])];
}


how do i use the subtract you gave me?

    
CVector *vec1 = // something
CVector *vec2 = // something else
CVector *vec3 = [vec1 subtract:vec2];


----

No offense intended, but you need to learn ObjC if you can't view a class and use one of its methods then... so go get a book and learn.

 "I will"

----

This is the LAST question:
    
//View.h
CVector *mazeWorld[1000];
//View.m

- (void)something
{
for(i = 0; i < g_NumberOfVerts; i++)
{
	float x,y,z;
	fscanf(levelfile, "%f %f %f\n", &x, &y, &z);
	mazeWorld[i] = [[CVector vectorWithX:x Y:y Z:z] retain];
	index++;
}
}
- (void)somethingelse
{
       glBegin(GL_TRIANGLES);
		for(i = 0; i < g_NumberOfVerts; i += 3)
		{
			glVertex3f([mazeWorld[i] x], [mazeWorld[i] y], [mazeWorld[i] z]);
			glVertex3f([mazeWorld[i+1] x], [mazeWorld[i+1] y], [mazeWorld[i+1] z]);
			glVertex3f([mazeWorld[i+2] x], [mazeWorld[i+2] y], [mazeWorld[i+2] z]);
		}
	glEnd();
}



The problem is that it doesn't output on the screen!     (Everything else does)

---- I don't know any OpenGL, but you aren't retaining the CVectors you put in the maze world array, so by the time you get around to drawing them the data is probably junk. Alternatively you could use an NS(Mutable)Array instead of a plain old C one, as collection objects retain whatever you put in them.

----

Ok now i'm retaining them, but it still doesn't output!

Check if i'm retaining them right (edited code)

----

Use the debugger. Set breakpoints. Insert logging statements. Take things into your own hands.

*I assume that you've investigated everything as far as actually setting up your OpenGL context and the like? Is your view a subclass of NSOpenGLView? (That's probably the easiest route, except for maybe GLUT.)*

Yes my view is a subclass of NSOpenGLView, and it is just the things that uses theCVector that doesnt show up.

Anyway i'm working the project from scratch now...

----

I...I...just don't know what to say. Wow. Zero to sixty mph in 11 seconds.

