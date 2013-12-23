---
layout: page
title: NSViewProblem
---



I would appreciate it if anyone would have a look a the following code and explain to me why:

�  in my  - (void)setTheLens: (Lens *)lens  method, the NSLog(@"lens name = %@, [theLens name]);  displays the lens name, an NSString, as expected, but

�  in my   - (void)drawRect:(NSRect)rect  method, the NSLog(@"theLensName = %@", theLensName);  displays (null).

Clearly the object is being passed to my NSView subclassed object.  Why is the drawRect not seeing it?

I'd be much obliged.


    
#import "LensReportView.h"

@implementation LensReportView

- (id)initWithFrame:(NSRect)frameRect
{
	if ((self = [super initWithFrame:frameRect]) != nil) {
		// Add initialization code here
		theLens = Lens alloc] init];
	}
	return self;
}

- (void)drawRect:([[NSRect)rect
{
	NSDictionary *attribs = [[NSDictionary alloc] init];
	NSPoint aPoint = NSMakePoint(20,20);
	NSString *theLensName = [[NSString alloc] init];
	
	theLensName = [theLens name];
	NSLog(@"theLensName = %@", theLensName);
	
	[[NSColor redColor] set];
	NSRectFill([self bounds]);
	
	[theLensName drawAtPoint: aPoint withAttributes: attribs];	
}

- (void)setTheLens: (Lens *)lens
{
	NSLog(@"setThelens");
	[lens retain];
	[theLens release];
	theLens = lens;
	NSLog(@"lens name = %@", [theLens name]);
	[self setNeedsDisplay: YES];
}
@end


----

The code in drawRect: is going to cause you problems in more ways than one. For the time being, it immediately leaks the NSString instance that you create with     NSString *theLensName = [[NSString alloc] init]; since in the very next line you point     theLensName at the instance returned from     [theLens name]. The variable     theLensName is a pointer to an instance of NSString, but it can point to any instance you decide to give it. As soon as the     theLensName = [theLens name]; line executes, the instance from the previous line (which has an address different from the one returned by     [theLens name]) will be lost forever, and with no hope of ever being able to release it. So never do something like this. Never. Never. Never.

We have no idea what the instance of NSString will come back from     theLensName = [theLens name];. According to your testimony, it is nil. It means the instance of Lens from which you are trying to get the name is not properly initialized.

For those reasons alone, I recommend you review your tutorials on ObjectCreation and MemoryManagement. There are many good ones. These subjects stand at the core of anything you will do in your future Cocoa programming, and you must master them yourself.

To answer your formal question, it will be not be possible, unless you reveal the code for how your Lens object is created. Do you give it a default name in its     init method? There is another MemoryLeak problem in the code above, since you create AnObject of the Lens class in your view's initWithFrame: method. You would release that one if you ever called setTheLens, but the new instance that you retain there will never be released, which you would do in the cleanup code for your NSView subclass. Whatever Lens object you are passing into setTheLens: is NOT the one you create in the initWithFrame: method. You never call     setTheLens: in the code we see above.

What in the world do you mean by *Clearly the object is being passed to my NSView subclassed object*? You create an InstanceObject of the Lens class within your NSView subclass. You could indeed pass in a Lens object, but we don't see you do it. Not "clearly", anyway.

So, no, you do not at first glance have an NSViewProblem, even though your problem is occurring inside an NSView subclass. You have a basic problem in understanding ObjectCreation and initialization.

This discussion should eventually be moved to a page with a title like MemoryManagementQuestion.

----

Here is the code that initializes a     Lens object:
    
@implementation Lens
- (id)init
{
	NSLog(@"initializing Lens object");
	shutterSpeeds = [[NSMutableDictionary alloc] initWithCapacity: 25];
	errors = [[NSMutableDictionary alloc] initWithCapacity: 25];
	return self;
}


Here is the code that invokes the     setTheLens method of my NSView subclassed object:
    
- (IBAction)makeLensReport: (id)sender
{
	NSLog(@"makeLensReport");
	LensReportView *theLensReportView = [[LensReportView alloc] init];
	
	[lensReportWindow makeKeyAndOrderFront: self];
	[theLensReportView setTheLens: currentLens];             // LOOK HERE: you're setting the lens AFTER you put the window in front!!!!!
}


Here are the results I'm seeing in the log console:
    
2006-04-03 09:53:15.091 ShutterSpeedData[11085] makeLensReport
2006-04-03 09:53:15.102 ShutterSpeedData[11085] theLensName = (null)
2006-04-03 09:53:15.105 ShutterSpeedData[11085] setThelens
2006-04-03 09:53:15.105 ShutterSpeedData[11085] lens name = lens1


----
I would add that the original poster should make sure the instance of LensReportView that receives the -setTheLens: message is the SAME instance that receives the -drawRect: message.  Time and time again we see people instantiating via code and creating a separate instance in IB or mistakenly instantiating two instances in IB.  Hint: every single time you drag an object off an IB palette, you are instantiating a new and unique instance of some class.  It is redundant and mistaken to ALSO instantiate the object via the Classes tab of the IB document window or through code.  [The glaringly obvious bogus memory management in the poster's code suggests a failure to understand the Objective-C concept of instances.]

----

*Good point* -- original respondent

----

*I moved the relevant portions of my last question down here, and eliminated some of the code you posted, which was not relevant*

If you get the output you expect in setTheLens:, it's because of the object you pass into that method, which may be initialized OK. It looks as if the Lens object in drawRect: is the one you create in the initWithFrame: method; but that is an assumption I make. **Does your Lens class have a method setName: or anything like that?** Put some logging code in your initWithFrame: method, and check out when it is executing as your app starts up with respect to the logging code in drawRect:.

I can now ask you this:  in makeLensReport:, where do you get currentLens? In your implementation of the Lens class, I do not see where you give the Lens object a default name. The logging that prompted your post is ostensibly only about the fact that you do not get a lens name at some point in your code.

Notice that I previously asked you if your Lens class has a setName: method. I recommend you also learn how to use the debugger, so we don't have to step through your code in public like this. You might want to study DebuggingTechniques when you get this thing straightened out. I know that at the beginning, it's all a bit bewildering, though. It's a process of calming yourself down, and beginning to step through your code, so you can see for the first time perhaps how you have actually implemented things. Writing the source often does not give you that second sight, since one gets so immersed in just typing the code.

It's the Lens name that is giving you fits. Where do you set the name for the thing? If you set the lens *after* you put the window in front (because that will cause your custom view to re-draw), how do you expect the view to have the lens you want when it draws itself? See the comment I placed in your code. So I'm beginning to agree with your premise, that is is an NSViewProblem, i.e., what a view needs to have set up when it draws itself. -- anonResp

----
I was under the impression that     theLens in my     setTheLens method was the same object as     theLens in my     drawRect method.  As to when the     drawRect gets executed vs. when     theLens is set, I thought that the fact that I called     [self setNeedsDisplay: YES] at the end of     setTheLens that the view would be redrawn at that point.  Clearly I'm wrong, but I don't understand why.

To be sure, there are many things that I don't understand.  I am making the earnest attempt to learn them all.  I read, attempt to code, make mistakes and, hopefully, learn from them.  I'm admittedly not the sort who can read three books, the documentation, then produce correct code.  Am I slow?  Perhaps, but along I plod...

If you stronger cocoa programmers are tiring of this newbie's questions, I understand.  Hopefully, though, someone out there will remember what it was like being a beginner at something...

Thanks  ;-) -- OP

----

*I was under the impression that     theLens in my     setTheLens method was the same object as     theLens in my     drawRect method.*

*To be sure, there are many things that I don't understand.  I am making the earnest attempt to learn them all.*

True, but who gives a fsck? Why won't you answer the question about how it is that you actually set the name ivar for your Lens objects? You have not done that. It is not that you are a beginner that ticks me off, but that you are being so obstinate about this. If you refuse to answer the question this time, you are being deliberately stubborn. Furthermore, you still have not characterized the currentLens object at all. Until you can demonstrate that you have properly initialized this object (which you have NOT so demonstrated) you get no respect. Wanking in public is not considered to be in good taste. You are starting to look like a genuine wanker. -- anonResp

----
Does no one else here fine the previous post to be unnecessarily rude and offensive?

Regardless of whether or not you think my questions are worthy of merit, there is no call for such a response.  You are certainly not forced to reply - or even read the post, for that matter.  Furthermore, I didn't post this question to earn your *respect*.  If you don't give a *fsck* then why on earth are you reading and responding to this post?


Thank you to all who have responded in a constructive, civil manner. -- OP

----
*well, if you've no interest in learning, don't keep asking questions! it's only a three-year-old who keeps on asking "why? why? why?" each time you answer his questions.*  -- anonResp

The only bug you have disclosed is one that indicates your model class is somehow not cooperating with the interface, and yet you publish only the init method of your model class (Lens), when it's an accessor for it that may be at fault. Whatever, the bug cannot be explained only by the code you've published. As another poster pointed out, your nib objects might be implicated as well.

----
Still, there is no need for name calling.  I suspect you would ignore such a three year old, no?


Consider that I didn't fully understand the questions being posed to me.  I posted code, some of which was deleted.  Was in unnecessary or irrelevant code?  Apparently.  In that case I'm not sure what to post.  Every point in my code where my lens object gets it's     name set?  I already posted the     init code for my Lens class but you deleted it so obviously it wasn't what you meant for me to show you.

Why would you attribute such sinister motives to my postings?  I guess you could think I'm being *obstinate* or *stubborn*.  However another explanation might be that I just don't know enough to respond appropriately.  If you know of another forum that might be better suited to questions for a person of my level of knowledge, I'd appreciate it if you would share that information. -- OP

----
**Cease fire! Hold off for a moment, please. I'm trying to get a word in edge-wise. -- KritTer**

----
*well, if you've no interest in learning, don't keep asking questions! it's only a three-year-old who keeps on asking "why? why? why?" each time you answer his questions.*


My last post was in response to that line.

I see that you added something constructive afterwards, which I appreciate.  I will go back and re-examine my code to see if the cause of the problem presents itself.  Thank you for your valuable time.


----
OK. I agree with the OP, anonResp's teenage obsession with the word "wank" has degenerated below what I would consider acceptable for CocoaDev. On the other hand, he does (in his more civil moments) have a valid point: you appear on the face of it to be spending too much time reading and replying to this page, and not enough learning about the subjects mentioned. Ease off on editing this page, too, it's impossible to comment with the edit war that's going on.

Try printing the address of your lens object in     setTheLens and     drawRect. If they differ, you've found your problem. -- KritTer

