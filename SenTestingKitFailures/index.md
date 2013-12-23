---
layout: page
title: SenTestingKitFailures
---

I am new to using the SenTestingKit provided by XCode and have been working through a simple project to gain experience.  The project contains two targets; a framework and its associated unit test bundle.  The unit test bundle contains a single objc test case class file with about five tests in it.  Everything works.

Adding a second, objc test case class (new .m/.h files) and immediately building, causes the following linking error.
     
Undefined symbols
  .objc_class_name_SenTestCase
  _STComposeString
  ...
  internal link edit command failed

Even though both the .m/.h contains no developer content; just the normal boiler plate!

The header file does contain the #import <SenTestingKit/SenTestingKit.h> statement and the interface is derived from SenTestCase.

Adding a test method to the new file that simply calls STFail() does not relieve the link failure.
Deleting the new files from the project allows it to build and run.

Why am I getting this linking error on a boiler plate, XCode provided, unit test file?
Thanks,

----

It looks like you added the second test case to your framework or application target, not to your unit test bundle target.  When you added the test case, in the alert sheet that Xcode brings up, you need to be sure that the appropriate target is checked.

(Also, just FYI, it's "Xcode," not "XCode.")

----

That was the problem.  I told you that I was new to this.  Thanks.

----

I am trying to use the SenTestingKit in a project that spawns off an NSTask for use with the tidy utility.  But the problem can be demonstrated with ls.

This works.
    
- (void)test_callingLsGood
{
    NSTask *task = [NSTask launchedTaskWithLaunchPath:@"/bin/ls" arguments:[NSArray array]];
    STAssertNotNil( task, @"Could not launch 'ls'" );
}


This crashes Apple's 'RunUnitTests' script.
    
- (void)test_callingLsBad
{
    NSTask *task = [NSTask launchedTaskWithLaunchPath:@"/bin/ls" arguments:[NSArray arrayWithObjects:@"-l"]];
    STAssertNotNil( task, @"Could not launch 'ls'" );
}

Error: "Test rig '/Developer/Tools/otest' exited abnormally with code 139 (it may have crashed)."



Experimenting with other unix commands in place of ls, I am beginning to convince myself that _any_ nonempty argruments array triggers the failure.  What is a test infected coder to do?
PriceRingo

----
You're missing the     nil at the end of your arguments list to     arrayWithObjects:.

----
You'll be interested in ChrisHanson's article on debugging unit tests: http://chanson.livejournal.com/120740.html

When it says that it may have crashed, it really means it. Just like any other code, get out the debugger and find out why.

