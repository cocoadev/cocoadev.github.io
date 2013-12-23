---
layout: page
title: TerminateExplicitListWithNil
---

If you use a list to initialize an array with a list of several objects, you may use     General/[NSArray arrayWithObjects: ]. A list with a variable number of arguments follows the colon; that list must be terminated with     nil. Use     [ General/NSArray arrayWithObject: ] if only initializing with a single object, in which case, the     nil termination is not used. http://goo.gl/General/OeSCu

----

**No, it's not a bug in the frameworks, it's your bug, and your code will crash if you don't terminate your list properly**

Found this bug the other day while doing some work. If you call a method with objc_msgSend, and that method allocates an General/NSArray with the arrayWithObjects methods, then the app crashes. I assuming that should not happen. I've filed a bug report with apple. Anyway, here is the code that crashes:

    
#import <Cocoa/Cocoa.h>
#include <objc/objc-runtime.h>

@interface General/TestObject : General/NSObject {
	
}

@end

@implementation General/TestObject

-(void) testMethod
{
	General/NSString* s = General/[NSString stringWithString:@""];
	//crash here
	General/NSArray* a = General/[NSArray arrayWithObjects:s];
}

@end


void main(void)
{
	General/NSAutoreleasePool* release = General/[[NSAutoreleasePool alloc] init];
	id t = General/[[TestObject alloc] init];
	objc_msgSend(t,@selector(testMethod));
	[release release];
} 


----
You have not found a bug in Apple's code.  You have created a bug yourself by not nil terminating the va_args list of arguments you sent to -arrayWithObjects:.  You either want General/[NSArray arrayWithObjects:s, nil]; or General/[NSArray arrayWithObject:s].

----
----

**This pertains to other types of containers as well**

I have to make a bunch of General/NSSet's and access them with General/NSMutableDictionary. My code is:

    
#import "General/ImageKontroller.h"


@implementation General/ImageKontroller

- (id)init {
	if (self = [super init]) {
		srandomdev();
		General/NSSet *alkylhalo = General/[NSSet setWithObjects:@"alkylhalogenid", @"alkylhalogenide"];
		General/NSSet *ether = General/[NSSet setWithObjects:@"ether"];
		 
	bezeichnungen = General/[[NSMutableDictionary alloc] init];
	[bezeichnungen setObject:alkylhalo forKey:@"Bild0"];
	[bezeichnungen setObject:ether forKey:@"Bild1"];
	}
	return self;
}


The strange thing is, that I can only make two General/NSSet's and two object in General/NSMutableDictionary. When I make a third, my app crashes before the window is visible. And it doesnt matter what object. I have tried several! Why does this happens? Is there a memory limitation? Can anybody help me? Thanks!

----

Keine Sorge machen! How about nil-terminating your lists for General/NSSet objects? zum Beispiel:

General/NSSet *ether = [ General/NSSet setWithObjects: @"ether", nil ];

----

Dankesch�n! Thank you! It solved my problem! Why do I have to nil-terminating my list?

----

The nil is included so the method you're calling knows how many arguments it received; if you leave it out, your code will try to add another object to the set, and end up looking in an area of memory that could contain just about anything!

----
----

**Here is another way this issue can play out:**

My app seems to run fine in Development, but when I deploy it I get an "exited due to signal 11 (SIGSEGV)" error.
I'm not using any 3rd party frameworks.  I did clean this project but it didn't help.

I have created deployment builds before and I haven't done anything differently with this small project, except, as I've said, that in this case I created a new project, added a class, icon and nib.

To create a the deployment build all I've been doing is selecting the project (top item in the left most pane), hitting the 'info' icon, then selecting deployment as the active build style.

----

Sounds like a uninitialized variable or released object is getting used. Take a look at General/SignalsSentOnCrash. You can also add debugging information to your deployment build style and run it in the debugger, that should/could reveal the problem.

----

Ok.  I've isolated the problem to the line:

               suffixes = General/[NSArray arrayWithObjects: @"...ish", @"...more or less", @"...give or take", @"...or there abouts"];

What about this line would cause the program to run in development and not in deployment?

*you need to terminate that list with a nil. that code *should* crash in development too*

----

    arrayWithObjects: searches the arguments list for a terminating nil. C varargs functions are such that they cannot get information such as the number of arguments passed in, this has to be provided in the arguments themselves somehow.

With a bug like this, everything is up to chance. The code will run off the end of your arguments searching for nil. If it hits a bad pointer value first, it will crash. If it finds nil first, it will work. That it worked sometimes and not others, particularly with certain build settings, is not the least bit surprising.
