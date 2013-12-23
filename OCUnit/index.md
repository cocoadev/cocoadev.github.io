---
layout: page
title: OCUnit
---



OCUnit, one of the first testing unit frameworks for any language is available at http://www.sente.ch/software/ocunit

A tutorial, "Unit Testing for Objective-C using ProjectBuilder and OCUnit" is available on Stepwise at http://www.stepwise.com/Articles/Technical/2002-06-17.01.html

see also SenTestingKit

**Note:**

The tutorial at Stepwise is **very old and uses deprecated macros**. Instead, you should use these macros

    STAssertNil(a1, description, ...)
STAssertNotNil(a1, description, ...)
STAssertTrue(expression, description, ...)
STAssertFalse(expression, description, ...)
STAssertEqualObjects(a1, a2, description, ...)
STAssertEquals(a1, a2, description, ...)
STAssertEqualsWithAccuracy(left, right, accuracy, description, ...)
STAssertThrows(expression, description, ...)
STAssertThrowsSpecific(expression, specificException, description, ...)
STAssertThrowsSpecificNamed(expr, specificException, aName, description, ...)
STAssertNoThrow(expression, description, ...)
STAssertNoThrowSpecific(expression, specificException, description, ...)
STAssertNoThrowSpecificNamed(expr, specificException, aName, description, ...)
STFail(description, ...)
STAssertTrueNoThrow(expression, description, ...)
STAssertFalseNoThrow(expression, description, ...)


These are all listed with comments in /System/Library/Frameworks/SenTestingKit.framework/Versions/A/Headers/SenTestCase_Macros.h

The code from the tutorial should look like this with the new macros:

    #import "TestPerson.h"
#import "Person.h"

@implementation TestPerson
- (void) testFullName
{
    Person *person = Person alloc] init];     
    [person setFirstName:@"Pablo"];
    [person setLastName:@"Picasso"];
    [[STAssertEqualObjects([person fullName], @"Pablo Picasso", nil);
    [person release];
}

- (void) testEmptyFirstName
{
    Person *person = Person alloc] init];    
    [person setFirstName:@""];
    [person setLastName:@"Picasso"];
    [[STAssertEqualObjects([person fullName], [person lastName], nil);
    [person release];
}

- (void) testNilFirstName
{
    Person *person = Person alloc] init];    
    [person setFirstName:nil];
    [person setLastName:@"Picasso"];
    [[STAssertEqualObjects([person fullName], [person lastName], nil);
    [person release];
}
@end


More info on unit testing can be found via google. You might want to read this http://java.sys-con.com/read/37795.htm since: *Test-Driven Development Is Not About Testing.*

