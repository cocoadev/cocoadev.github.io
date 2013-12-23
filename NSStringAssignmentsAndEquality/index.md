---
layout: page
title: NSStringAssignmentsAndEquality
---

What's wrong with this code?

    
NSString *string = [NSString stringWithString:@"hello world"];
if (string = @"hello world") NSLog(@"hello world");


An assignment operator is used to assign a  value to a variable (e.g.     NSString *string = @"hello world").

An equality operator is used to test a condition of equality (e.g.     if (x == 10) NSLog(@"x is equal to 10")).

The line that tests a string to see if it is equal to another string has two mistakes ->     if (string = @"hello world")

The first problem with this line is an assignment operator is being used to test for equality. The line should look like ->      if (string == @"hello world")

The other problem is one needs to understand what object pointers are.     string is a pointer to an object. This variable is a memory address. Equality operators can only test for the equality of the basic C types (e.g.     int, float,  short, char, unsigned int...). Equality operators CAN test for the equality of a pointer (    if (string == anotherSubstring) [anObject doSomething]), but what you are testing for in this conditional statement is the equality of two memory addresses not the equality of two strings. 

Try the following code:

    
    NSString *string1 = @"string";
    NSString *string2 = @"string";
    NSString *string3 = [NSString stringWithString:@"string"];
    NSString *string4 = [NSString stringWithString:@"string"];
    
    NSLog(@"address for string1: 0x%08x address for string2: 0x%08x", string1, string2);
    NSLog(@"address for string3: 0x%08x address for string4: 0x%08x", string3, string4);

    if (string1 == string2) NSLog(@"string1 address equals string2 address");
    else NSLog(@"string1 address does not equal string2 address");
    
    if (string3 == string4) NSLog(@"string3 address equals string4 address");
    else NSLog(@"string3 address does not equal string4 address");
    
    if ([string1 isEqualToString:@"string"]) NSLog(@"string1 is equal to the string: \"string\"");
    if ([string2 isEqualToString:@"string"]) NSLog(@"string2 is equal to the string: \"string\"");
    if ([string3 isEqualToString:@"string"]) NSLog(@"string3 is equal to the string: \"string\"");
    if ([string4 isEqualToString:@"string"]) NSLog(@"string4 is equal to the string: \"string\"");



The first four lines appear to be assigning the same value to the NSString pointers     string1, string2, string3 and     string4

    
    NSString *string1 = @"string";
    NSString *string2 = @"string";
    NSString *string3 = [NSString stringWithString:@"string"];
    NSString *string4 = [NSString stringWithString:@"string"];


What is actually going on here is     string1 and     string2 are being assigned the value for a memory address that points to a globally shared string object for the string "string". The line 

    
NSLog(@"address for string1: 0x%08x address for string2: 0x%08x", string1, string2); 


reveals that     string1 and     string2 are the same memory address. The output for this line is:

    
2003-11-28 07:59:28.596 test[830] address for string1: 0x0009a000 address for string2: 0x0009a000


    string3 and     string4 are being assigned two different instances of a string object that contains the string "string". The line 

    
NSLog(@"address for string3: 0x%08x address for string4: 0x%08x", string3, string4);


reveals that     string3 and     string4 do not have the same memory address. The output for this line is:

    
2003-11-28 07:59:28.598 test[830] address for string3: 0x0030d5c0 address for string4: 0x0030d950


This is one of the easiest things to get confused with when learning Objective C. You have to understand that an object pointer is only a memory address and when you test to see if one object pointer is (    <, >, <=, >= or ==) to another object pointer, you are only testing the condition of the memory address of these object pointers. The lines 

    
if (string1 == string2) NSLog(@"string1 address equals string2 address");
else NSLog(@"string1 address does not equal string2 address");


tests to see if the address to     string1 is equal to the address of     string2. The output for this line is:

    
2003-11-28 07:59:28.598 test[830] string1 address equals string2 address


This makes sense because both     string1 and     string2 point to the same globally shared string object for the string "string". The lines 

    
if (string3 == string4) NSLog(@"string3 address equals string4 address");
else NSLog(@"string3 address does not equal string4 address");


tests to see if the address to     string3 is equal to the address of     string4. The output for this line is:

    
2003-11-28 07:59:28.598 test[830] string3 address does not equal string4 address


the memory address for     string3 does not equal the memory address for     string4, so you would expect this conditional statement to be false when using the statement     (string3 == string4). 

If you want to test the equality of strings you have to ask one string object to see if it is equal to another string object. This is true with all Objective C objects. If you want to see if one object is eqaul to another object, there has to be a method implementation to perform this operation because all objects will not be able to test for equality with a simple     == operator. NSNumbers are able to be tested for equality with the     == operator, but you have to access the encapsulated values first before you can test two NSNumber objects for equality

    
NSNumber *aNumberObject = [NSNumber numberWithInt:6];
NSNumber *anotherNumberObject = [NSNumber numberWithInt:6];
if ([aNumberObject intValue] == [anotherNumberObject intValue]) NSLog(@"aNumberObject is equal to anotherNumberObject");


Strings fall into the group of objects that are unable to test for equality with a simple     == operator, so the class "NSString" has to implement a method to test for equality. This method is "isEqualToString:". NSString will check every character in one string against every character in another string before deciding if the two strings are equal. 

So, if you test all of the strings above to see if they equal the string "string", you will see that the sequence of characters of one string is equal to the sequence of characters for the string "string". Therefore the lines

    
if ([string1 isEqualToString:@"string"]) NSLog(@"string1 is equal to the string: \"string\"");
if ([string2 isEqualToString:@"string"]) NSLog(@"string2 is equal to the string: \"string\"");
if ([string3 isEqualToString:@"string"]) NSLog(@"string3 is equal to the string: \"string\"");
if ([string4 isEqualToString:@"string"]) NSLog(@"string4 is equal to the string: \"string\"");
 

will log that     string1, string2, string3 and     string4 are all equal to the sequence of characters for the string "string". The output is:

    
2003-11-28 07:59:28.598 test[830] string1 is equal to the string: "string"
2003-11-28 07:59:28.599 test[830] string2 is equal to the string: "string"
2003-11-28 07:59:28.599 test[830] string3 is equal to the string: "string"
2003-11-28 07:59:28.599 test[830] string4 is equal to the string: "string"


--zootbobbalu

