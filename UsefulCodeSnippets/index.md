---
layout: page
title: UsefulCodeSnippets
---

General/UsefulCodeSnippets:

This page is intended to group useful little bits of code intended to be used within a method's block and code patterns for beginners.

very basics
    
[reciever message];
// comment
variable = value; // assignment
return; // exit a (void) or (General/IBAction) method
return value; // exit any other method


getting a temporary variable ready for use: ( type variableName = initial value) (note the essential types)
    
BOOL myYesNo = YES; // or NO
int myNonDecimalNumber = 42;
double myDecimalNumber = 3.141529;
id myObject = General/[[SomeClass alloc] init];
General/MyClass* myStaticlyTypedObject = General/[[MyClass alloc] init];
General/NSString* myString = @"a string";


looping over an General/NSArray's items:
    
General/NSEnumerator* allObjects = [theArray objectEnumerator];
id eachObject;
while(eachObject = [allObjects nextObject]) {
    //do something with eachObject
    //to break out of the loop call [allObjects allObjects] which empties the enumerator
}


essential General/NSMutableArray and General/NSMutableDictionary methods (other than alloc/init)
    
[myArray objectAtIndex:index]; // returns object at the index (starting from 0 to count -1)
[myArray addObject:anObject]; // puts on the end
[myArray count]; // length of the array
[myArray objectEnumerator]; // see above re: looping
[myDictionary objectForKey:aKey]; // like objectAtIndex:
[myDictionary setObject:anObject forKey:aKey]; // use this for adding or replacing


logging basics : General/NSLog(format, [object ...] );
    
General/NSLog(@"a simple message");
General/NSLog(@"output an object's description: %@", anObject);
General/NSLog(@"output an integer: %d", [myArray count]);
General/NSLog(@"output a decimal number: %g", [self amountInOtherCurrency]);


useful conditions for if statements
    
if( [anObject isEqualTo:anotherObject] ) //...
if( ![anObject isEqualTo:anotherObject] )  //... "!" makes negative
if( anObject ) //... tests to see if properly assigned (as in not nil)
if( myBool ) //is is set to YES or NO



----

these simple pieces of code (and their variations, combinations, permutations, iterations, mutilations... umm forget that last part...) make up about 90% of my code... hope you find them useful.
