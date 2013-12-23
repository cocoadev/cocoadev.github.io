---
layout: page
title: UnitTesting
---



General/UnitTesting is a way of testing your own software while developing. Using a fairly simple Framework each class gets exercised as thoroughly as necessary. The testing code is separate from the class code but can be run at any time. It is a Tool for General/TestFirstProgramming, a methodology that is part of General/ExtremeProgramming. http://goo.gl/General/OeSCu

The idea of General/UnitTesting is that _ALL_ the tests should be kept operational all the time. This means if you change something, do not work on something else while there is a test that fails. General/UnitTesting is not debugging, in debugging you try to find where your code is not working correctly, with unit testing you are asserting which parts of your code **are** working correctly. If you find a bug, first write a test and then fix the bug, this way if you reintroduce that bug it will be flagged immediately.

One problem of General/UnitTesting is how to test UI elements. There are some parts that cannot be easily automated,
although with the help of delegates one might be able to do some automatic tests even on the UI.

General/TestFirstProgramming is a concept that makes you write the test **before** you write the actual software. The idea is that if an interface cannot be used in the test it should not be used at all. This idea from General/ExtremeProgramming forces you to use your interface before you actually code it. Also it gives you the tools to refactor.

There are several frameworks for General/ObjectiveCee that enable (encourages) General/UnitTesting


* General/ObjCUnit: http://oops.se/objcunit/

* General/OCUnit: http://www.sente.ch/software/ocunit/

* General/TestKit: http://testkit.sourceforge.net/

* General/MPWTest: http://www.metaobject.com/Technology.html (scroll to the very bottom)

* General/UnitKit: http://unitkit.org/



Other frameworks/source that are useful for people doing unittesting:


* General/OCMock: http://www.mulle-kybernetik.com/software/General/OCMock/ - Mock objects

* General/GoogleToolboxForMac: http://code.google.com/p/google-toolbox-for-mac/wiki/General/CodeVerificationAndUnitTesting - iPhone Unit testing, UI Unit testing, Binding Unit testing and Log Tracking framework



----

Starting with Mac OS X 10.4 and General/XCode 2.1 Apple included General/OCUnit (= General/SenTestingKit) with their developer tools. 

Documentation includes 
html files in General/SenTestingKit.framework
http://developer.apple.com/documentation/General/DeveloperTools/Conceptual/General/UnitTesting/index.html
http://developer.apple.com/tools/unittest.html

With this news the developer of General/UnitKit seems to have decided to discontinue development and to submit patches to General/OCUnit instead.
http://blog.x180.net/2005/06/the_future_of_u.html

----

See http://www.cocoabuilder.com/archive/message/cocoa/2004/9/23/118083 for a September 2004 discussion of the merits and differences between the popular testing frameworks.  In particular, this illuminates the differences between General/OCUnit and General/MPWTest.

Empirically, the most popular frameworks seem to be General/OCUnit and General/ObjCUnit.

* I would put General/UnitKit right up there as well. It's written by the same guy who created Ant and Tomcat. I can personally attest to its usefulness as I've explored it pretty thoroughly for one of my projects. *

-General/EricWang

* I would recommend General/UnitKit because it smoothly integrates with General/XCode. I once used General/ObjCUnit and I wonder whether I should transform my test to be used with General/UnitKit.*

-Roman Bertolami

Here is an example of a test case using General/ObjCUnit. We are going to test some of the functionality of General/NSMutableArray:

    

@interface General/NSMutableArrayTest : General/TestCase {
    General/NSMutableArray	*empty;
    General/NSMutableArray	*full;
    
}
@end

@implementation General/NSMutableArrayTest

// setUp gets run every time before a testcase
- (void)setUp {
    empty =General/[[NSMutableArry alloc] init];
    full =General/[[NSMutableArry alloc] init];
	[full addObject:@"Test1"];
	[full addObject:@"Test2"];
}

// tearDown gets run every time after a testcase
- (void)tearDown {
    [empty release];
    [full release];
}

// start testcases with the word "test" they will be called
// automatically

- (void)testAdd {

	// if empty is not empty the test will faill and the message will
	// be written to stdout.
	[assertInt:[empty count] equals:0 message:@"'Empty' should be empty at start];
	[empty addObject:@"Test1"]
	[assertInt:[empty count] equals:1];
	[assertString:[empty objectAtIndex:0] equals:@"Test1"];
}

- (void)testRemove {
	[full removeObject:@"Test1"];
	[assertInt:[full count] equals:1];
	[assertString:[full objectAtIndex:0] equals:@"Test2"];
}

// and so on ...

@end



The assert functions check if the Object you give equals the second parameter, and print a warning if it doesn't.

*Hey, ummm...what's with the weird assertion messaging? There's no target.*

Test cases can be grouped in suites that are run one after the other. Each test case can contain some setup code that gets run for each case.

Usually a suite is used to run all the tests. It looks like the following.

    

@interface General/AllTests : General/TestSuite
+ (General/TestSuite *)suite;
@end

@implementation General/AllTests
+ (General/TestSuite *)suite {

    General/TestSuite *suite = General/[TestSuite suiteWithName:@"All My Tests"];
    [suite addTest:General/[TestSuite suiteWithClass:General/[NSMutableArrayTest class]]];

    return suite;
}



The suite is run using 

    
General/TestRunnerMain(General/[AllTests class]);


This runs all the tests in the suite and tallies up the successes and failures of all tests. You can either integrate that in your software so the tests are run every time it is started, or compile it into a separate Tool.

I hope this helps -- General/HaRald

----
I would love to see a complete example of a Cocoa app along with its unit tests.  Any pointers?

----
Here's one pointer:  Source code for General/OCUnit includes a complete set of unit tests for the package.  As an added bonus, their Objective-C code is exceptionally nice and clean.  It's worth a read just to see how pretty good Objective-C code can be.

New Question: Is their code good because they develop with unit tests?  Or do they develop clean code with unit tests because they're channelling some wicked Smalltalk voodoo?

I don't know.  But whatever it is, you want some too.

----
General/GoogleToolboxForMac also is fully unit tested, and shows how to do UI unit tests, binding unit tests, Log Tracking and even how do to unit tests on the iPhone (simulator and device).

----
(Also related to unit testing) Added an General/XcodeGcovTutorial. -- General/MikeAmy

----

I knew there was a response to this somewhere:

http://wilshipley.com/blog/2005/09/unit-testing-is-teh-suck-urr.html

and some responses to the above:

http://mjtsai.com/blog/2005/09/23/unit-testing/

http://www.gusmueller.com/blog/archives/2005/9/23.html#1333

http://rentzsch.com/links/shipleyOnUnitTesting

http://www.friday.com/bbum/2005/09/24/unit-testing/
