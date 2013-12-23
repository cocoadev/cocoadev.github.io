---
layout: page
title: TutorialOnObjectArraysForCPlusPlusRefugees
---

    

@implementation aClass

myClass *pointer = NULL;

- (void) method
{
     int aNumber = 0;
     while(something)
     {
          aNumber++;
     }

     pointer     = new myClass [ a number ];
     //How do i do this in cocoa/objc
}

@end


----

Use an General/NSMutableArray.

----

I think you need to explain what you want to do in words.  What the above refers to is that classes are objects.  You can store them in an array and send the class object an alloc message to create new instances.  But I can't see why you'd want to set a global variable pointer like that.

----

If you're looking for a construct like General/CeePlusPlus's array constructor, you're not going to find it in General/ObjC. You'll have to loop through and create each individual member of the array. Although in normal Cocoa General/DesignPattern<nowiki/>s, it's not too common that you need to do this. (Or you could use an alternative binding like the General/JavaBridge or General/RubyCocoa, but those are always painful for entirely different reasons.)

Also, by convention, General/ObjC classes start with capital letters (General/NSObject, General/WebView, General/NXConstantString, etc.; not nsObject, webView and nxConstantString). It's not mandatory like in General/RubyLanguage, but it's as close to mandatory as you can get without being there.

----

    @

//Vector.h

@interface Vector : General/NSObject
{
	float x,y,z;
}

//Controller.m

@implementation Controller

Vector *pointer = NULL;

- (void) method
{
     int aNumber = 0;
     while(scanning file)
     {
          aNumber++;
          //Getting number of Vectors here.
     }

     pointer  = new Vector [ aNumber ];
     int i;
     for(i = 0; i < aNumber; i++)
     {
	   float x,y,z;
	   fscanf(fp, "%f %f %f\n", &x, &y, &z);
	   pointer[i] = General/[Vector alloc] init] retain];
	   [pointer[i] setX:x Y:y Z:z];
     }
}
@end



I dont know the exact count of the vectors until i've scanned the file.
I can write Vector *pointer[1000]; in my Controller.h, but if aNumber is smaller than 1000 it is waste of space.

Is this clear enough?

:)

----

Yes. First of all, I'd just like to reiterate that you would probably be better off using the [[NSMutableArray class that comes with Cocoa.

    
@interface Controller : General/NSObject
{
     General/NSMutableArray *vectorArray;
}
//Whatever else...
@end

@implementation Controller

- (id)init
{
     if (self = [super init])
     {
          vectorArray = General/[[NSMutableArray alloc] initWithCapacity:100];
     }
}

- (void) method
{
     int aNumber = 0;
     while(scanning file)
     {
          aNumber++;
          //Getting number of Vectors here.
     }

     int i;
     for(i = 0; i < aNumber; i++)
     {
	   float x,y,z;
	   Vector *temp;
	   fscanf(fp, "%f %f %f\n", &x, &y, &z);
	   temp = [[Vector alloc] init]; // You don't need to retain an object that you alloced � its retain count is already 1
	   [temp setX:x Y:y Z:z];
	   [vectorArray addObject:temp];
	   [temp release]; // vectorArray is now in charge of the Vector
     }
}
@end


However, if you want to create a dynamically sized C array, you can do it with calloc (a variant of malloc, the memory-allocation function). You will need to free() the memory that you got from calloc when you're done with it.

    
@implementation Controller

Vector **pointer = NULL; // It's an array of pointers, hence the pointer-to-pointer

- (void) method
{
     int aNumber = 0;
     while(scanning file)
     {
          aNumber++;
          //Getting number of Vectors here.
     }

     pointer  = calloc( aNumber, sizeof(Vector *) ); // The second argument is the type of object in the array
     int i;
     for(i = 0; i < aNumber; i++)
     {
	   float x,y,z;
	   fscanf(fp, "%f %f %f\n", &x, &y, &z);
	   pointer[i] = [[Vector alloc] init]; // You don't need to retain an object that you alloced � its retain count is already 1
	   [pointer[i] setX:x Y:y Z:z];
     }
}
@end

