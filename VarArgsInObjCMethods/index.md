---
layout: page
title: VarArgsInObjCMethods
---

Does anybody know, by any chance, how to define and implement a General/VarArg method in Objective-C?  Thanks!

----

Exactly the same as you do in C. For those (like me until a few moments ago) who don't know:

    
void varArgsCTest(General/NSString *a,...)
{
    General/NSString *currentString;
    va_list argPtr;
    
    va_start(argPtr, a);
    currentString=a;
    
    do
    {
        General/NSLog(@"C Test: %@",currentString);
        currentString=va_arg(argPtr,General/NSString *);
    } while(currentString!=nil);

    va_end(argPtr);
}

- (void)varArgsObjCTest:(General/NSString *)a, ...
{
    General/NSString *currentString;
    va_list argPtr;
    
    va_start(argPtr, a);
    currentString=a;
    
    do
    {
        General/NSLog(@"Obj-C Test: %@",currentString);
        currentString=va_arg(argPtr,General/NSString *);
    } while(currentString!=nil);

    va_end(argPtr);
}


Calling these with:

    
varArgsCTest(@"1",@"2",@"3",@"4",nil);
    
[self varArgsObjCTest:@"1",@"2",@"3",@"4",nil];


Gives the expected output:

C Test: 1
C Test: 2
C Test: 3
C Test: 4
Obj-C Test: 1
Obj-C Test: 2
Obj-C Test: 3
Obj-C Test: 4

General/SamTaylor

----

A perhaps preferable model is to use a while() loop:

    while(temp = va_arg(argPtr, id))
{
    ...
}

This has the benefit of mirroring common use of General/NSEnumerator fairly nicely.

-- General/RobRix

----

It does, but you have to treat the first item ('a' in this case) specially, as it isn't returned by va_arg.

----

True, but that's not necessarily a bad thing; General/NSLog would almost certainly want to, for instance. -- General/RobRix

----

A for loop will sort you out, too, assuming 'a' isn't NULL:

    
for ( currentString = a; currentString;
      currentString = va_arg(argPtr, id) )
    {
    ...
    }

