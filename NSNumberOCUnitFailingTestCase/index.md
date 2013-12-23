---
layout: page
title: NSNumberOCUnitFailingTestCase
---

I'm trying to get into Objective-C coming from Ruby and figured the best way to make sure I had head screwed on straight was to start writing simple unit tests to check my assumptions against reality.

I got two tests into the process and ran into problems with the following test:
    
- (void) testNumberWithFloat
{
	NSNumber *number = [NSNumber numberWithFloat:10.5];

	STAssertEqualsWithAccuracy(10.5, [number floatValue], 0.1,
							 @"Number should equal 10.5 but instead equals %g.",
							 [number floatValue]);
	
	[number release];
}


It fails with the following error:
    
TestNSNumber.m:32: error: -[TestNSNumber testNumberWithFloat] : Type mismatch -- Number should equal 10.5 but instead equals 10.5.
RunUnitTests:153: error: Test rig '/Developer/Tools/otest' exited abnormally with code 139 (it may have crashed).


I'm utterly stumped and am surely missing something so blindingly obvious I simply can't see it. I'd be very grateful for a pointer in the right direction. Thanks!

--DaneJensen

----

Not sure, but try comparing against 10.5f.  10.5 is a double in C.

----

Thanks! That fixed the error with the assertion. 

Unfortunately, otest is still crashing.

----

You should not release     number, as you have not alloced, copied, or retained it. See MemoryManagement.

----

That did the trick. Thank you! I was right that I was missing some really basic things. Thanks for your patience.

