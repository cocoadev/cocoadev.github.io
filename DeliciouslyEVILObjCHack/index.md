---
layout: page
title: DeliciouslyEVILObjCHack
---

The question the other day regarding Objc Objects and structs got me to thinking...

I've always enjoyed the contrast between the CeePlusPlus and ObjectiveCee camps' respective outlook on efficiency of execution vs efficiency of implementation.

I was pondering all the time that ObjectiveCee spends allocating memory - and that the heap is the only 'legal' place for objects to live, when I decided to try a REALLY evil hack when I got home to see if it would work.

My trivially simple case does work, and I thought I'd share it. First the code, then the explanation.

    

@interface MyClass:NSObject
{
    int myScalar;
    NSString* myString;
}

-( void ) setMyScalar:( int ) anInt;
-( int ) getMyScalar;

-( void ) setMyString:( NSString* ) string;
-( NSString* ) getMyString;

@end

@implementation MyClass
-( void ) setMyScalar:( int ) anInt
{
    myScalar = anInt;
}

-( int ) getMyScalar
{
    return myScalar;
}

-( void ) setMyString:( NSString* ) string
{
    myString = string;//I'm purposefully not calling copy or retain - explanation below
}

-( NSString* ) getMyString
{
    return myString;
}

-( NSString* ) description
{
    NSMutableString *result = [ NSMutableString stringWithString:@"----MyClass----\n" ];

    [ result appendFormat:@"myScalar = %i\nmyString =%@\n------------", myScalar, myString ];

    return result;
}
@end

typedef struct
{
    @defs( MyClass );//ObjectiveC compiler directive to create a struct declaration list.
                              // So now we have a definition of the C structure for MyClass
}MyClassStruct;

int main( int argc, char* argv[] )
{
    MyClassStruct classStruct; //now I have the storage space for my class on the STACK
    MyClass          *myClass = (MyClass*)&classStruct; //cough...wheeeze...

    classStruct.isa = [ MyClass class ]; //HACK, HACK - DANGER WILL ROBINSON, DANGER!

    [ myClass setMyScalar:3 ];
    [ myClass setMyString:@"EVIL, EVIL BAD, BAD" ];

    NSLog( @"%@", myClass );

    return 0;

}


The class definition is plain vanilla. the @defs directive isn't used often, but it's not rocket science either - give me a type representing my class as a C struct.

So in main, I declare on the stack the same memory chunk that would be allocated via [ MyClass alloc ] on the heap - but it's ON THE STACK. zero overhead. Next I fake out a reference to MyClass by declaring it and setting it to the address of the previously declared struct. Then I set the isa member ( which lives on all ObjectiveC objects ) to the class it's representing.

I've just put a functioning objective-C object on the stack. And they said it couldn't be done... ;)

CAVEAT EMPTOR, CAVEAT EMPTOR....
This is bad. Note my comment above that I didn't retain/copy the string that was passed into the 'setString' method. Since I'm allocated on the stack, I don't have any memory management concerns. I no longer participate in the reference counting scheme. The problem is I had to know at compile time that MyClass would only ever be allocated on the stack. It's possible to fake this info out by detecting whether or not 'alloc' was ever called when you sprang to life, but I can see the waters between what should/shouldn't get reference counted getting muddy way, way to fast.

Note also that the initializer never gets called in my example. Probably could be called, but if this were a Foundation class that participated in a ClassCluster type scheme, then it'd probably end up in a not-so-pretty situation... 

Still - it does demonstrate the power of the language. The assumption is that programmers need tools, and are smart enough to use the BFG9000 with care.

But please - if you implement this - it's not my fault when you shoot yourself in the foot... ;) -- TimHart

