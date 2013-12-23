---
layout: page
title: NSArrayProblems
---

Xcode is telling me that NSArray doesn't respond to arrayWithContentsOfFile:? You know, that method that you can find in NSArray's documentation?  (Editor's note: selective boldfacing of this thread makes it more educational, and less snarky.)

----

How are you calling it?  It's likely either a misspelling, conceivably a **failure to import <Cocoa/Cocoa.h>** or, if you're new at this, a misunderstanding of class vs. instance methods.

----

A little "tough love" here: Posting a sarcastic question about an error message you're getting and then not showing us any code is not a good way to get answers. We can't really respond unless we can see what code is causing the compiler to be upset.

----

http://www.catb.org/~esr/faqs/smart-questions.html#beprecise

http://www.catb.org/~esr/faqs/smart-questions.html#id3001405

see also HowToAskQuestions

----

**As it turns out, it was because only AppKit.h was being imported**, I actually fixed it before reading this, and at the time that I posted, I was really pissed because of other problems the app was having, and I didn't even think about it.

Thanks anyway.

----

**Retain-Release problem in an NSArray of NSString** (formerly entitled IA**'mMissingSomething)

My controller object has a property defined as:

    NSString *employeeDetailsArray[40];

I have a series of NSButton objects that are connected to the following method:
    
- (IBAction)pBondButton:(id)sender
{
     NSLog(@"pBondButton has been clicked");
     [selectedPersonField setStringValue: @"Phil"];
     [detailsField setStringValue: employeeDetailsArray[14]];
}

All connections have been made.
Here's the problem:

-The NSLog() call executes fine.
-The line [selectedPersonField setStringValue: @"Phil"];  executes fine.
-The line  [detailsField setStringValue: employeeDetailsArray[14]];  crashes (exited due to signal 10 (SIGBUS)

To complicate matters:
- if I substitute the third line with [detailsField setStringValue: @"test"];  it works fine.
- if I put the line [detailsField setStringValue: employeeDetailsArray[14];   in another, non (IBAction) method, it works fine.

What am I not getting?

Thanks,
Phil

----

If your IBAction method calls the non-IBAction method does it work?  Could it have anything to do with the auto release pool? -- DaveTauzell

Are you positive that employeeDetailsArray[14] has been initialized before use? Debug the program, and when SIGBUS happens, go back up to your method (or you could just put a breakpoint there) and check to make sure you've got a valid value in there. --KevinPerry

----

Thanks for the reply.

It does NOT work when I have my IBAction method call the other.  That's another thing that puzzles me.  I admit, I'm not familiar with the debugger and now's a great time to look into it.    And yes, I'm quite sure the array is getting initialized prior to use. After all, it does work when I access it from other methods.

Phil

----

Nevermind. Solved it. **I guess the array elements were getting destroyed at some point and I had my retain in the wrong place.**  It will be a while before I get a clear handle on garbage collection, I'm sure.

Thanks,
Phil

----

Perhaps I was reading it wrong, or NSTextField setStringValue does indeed take NSString* type arguments.  It looked to me that your created NSString pointer objects, and then tried to pass the address of one of those objects to setStringValue.  Perhaps (NSString *)employeeDetailsArray[14] as the argument would not have necessitated a code change? ... -- AgtAlpha
----
No, the above code is correct and besides, every element in the array is an NSString pointer, which is the only way to refer to Objective-C objects. Each index in the array will point to an NSString (i.e. an NSString *) and when obtained with the array[i] notation will act just like an NSString *. --KevinPerry

----
Indeed, it's not too different from any other situation you're using nested references--a bit of a headache but manageable as long as you know how deep you can go.

Now, perhaps we need a comprehensive and comprehensible list of SIGs on here somewhere. The machine's giving you info by what signal it sends, so it'd be wise to pay attention to it. I *think* SIGBUS usually means "something got released behind your back" or for me, "you're using autorelease improperly again, doofus--retain things when you need to guarantee their survival!"

----

see also SignalsSentOnCrash

see also MemoryManagement

