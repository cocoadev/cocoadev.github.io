---
layout: page
title: NSNumber
---

**Class Description**

NSNumber is a subclass of NSValue that offers a value as any C scalar (numeric) type. It defines a set of methods specifically for setting and accessing the value as a signed or unsigned char, short int, int, long int, long long int, float, or double or as a BOOL. It also defines a     compare: method to determine the ordering of two NSNumber objects.

----

**Apple Documentation**

https://developer.apple.com/library/mac/#documentation/Cocoa/Reference/Foundation/Classes/NSNumber_Class/Reference/Reference.html

----
**See Also**

*NSDecimalNumber
*FakeNSNumbers (just for fun)


----

**Discussion**

A natural question is 'What kind of scalar value did a particular instance of NSNumber come from'?  NSNumber is a ClassCluster of more specific classes, and the identity of the subclass can be used to make this determination.  For example, an NSNumber that comes from a BOOL will be of the class NSCFBoolean whereas an integer will be an NSCFNumber.  The following snippet distinguishes the two:

    
 // suppose myNum is an instantiated NSNumber coming from an int or a BOOL
 if (myNum className] isEqualToString:@"NSCFNumber"]) {
      // process NSNumber as integer
 } else if  ([[myNum className] isEqualToString:@"NSCFBoolean"]) {
      // process NSNumber as boolean
 }


Alternatively:

    
 // suppose myNum is an instantiated NSNumber coming from an int or a BOOL
 if ([myNum isMemberOfClass:[NSCFNumber class) {
      // process NSNumber as integer
 } else if  ([myNum isMemberOfClass:[NSCFBoolean class]]) {
      // process NSNumber as boolean
 }


*You can also send     [NSNumber objCType] to get the type. Using className seems kind of fragile. See the docs for NSValue.* 

Using objCType is difficult and limited. The objCType returned for an NSNumber created with +numberWithBool is just char, which isn't helpful.  Also, the docs warn that the types are implementation dependent.

Also note that NSNumber is toll free bridged with CFNumber.  See TollFreeBridging.

*
I believe that this is not true.  Apple has a list of bridged classes here: http://developer.apple.com/documentation/Cocoa/Conceptual/CarbonCocoaDoc/cci_chap2/chapter_2_section_5.html
*

The article is now at http://developer.apple.com/documentation/Cocoa/Conceptual/CarbonCocoaDoc/Articles/DataTypes.html#//apple_ref/doc/uid/20002401 and does not mention NSNumber.  However, http://developer.apple.com/documentation/CoreFoundation/Reference/CFNumberRef/Reference/reference.html indicates that the Number classes are bridged, and NSNumber -className returns @"NSCFNumber".

Example Usage

    
 int i = 22;
 float f = 28.5;
 NSNumber *myInt;
 NSNumber *myFloat;
 myInt = [NSNumber numberWithInt:i];
 myFloat = [NSNumber numberWithFloat:f];
 NSLog(@"My float is %@ and my int is %@", myInt, myFloat);


--CharlieMiller

