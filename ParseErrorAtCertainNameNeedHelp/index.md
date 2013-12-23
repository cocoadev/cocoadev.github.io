---
layout: page
title: ParseErrorAtCertainNameNeedHelp
---

**ParseError because of old-style compiler prohibitions on declaring variables after executable code starts**

I have an error message :

"parse error"       before   
 " NSString * title = [song substringToIndex:range.location];"
and     "variale title is not declared"
     "variale artist is not declared". 

I know it might because I'm using OS10.1, but anyone knows how to do it right?(in OS10.1)

RedMac
  
-------------source code---------------------
    
#import <Foundation/Foundation.h>

int main (int argc, const char * argv[]) {
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];

    NSString * song = @"let forever be, the chemicla broters";
    NSRange range = [song rangeOfString: @","];
    printf("comma location is: %i\n", range.location);
    
    NSString * title = [song substringToIndex:range.location];
    printf("title is : %s\n", [title UTF8String]);
    
    NSString * artist = [song substringFromIndex: range.location + range.length];
    printf("artist is : %s\n", [artist UTF8String]);

.....

----

This is because you are declaring NSString after calling functions. You aren't allowed to do this in C or Obj-C. Put the title and artist variables up at top, and set them later.

Like this:

    
#import <Foundation/Foundation.h>

int main (int argc, const char * argv[]) {
    NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];

    NSString * song = @"let forever be, the chemicla broters";
    NSRange range = [song rangeOfString: @","];
	NSString * title, * artist;

    printf("comma location is: %i\n", range.location);
    
    title = [song substringToIndex:range.location];
    printf("title is : %s\n", [title UTF8String]);
    
    artist = [song substringFromIndex: range.location + range.length];
    printf("artist is : %s\n", [artist UTF8String]);


-- KentSutherland

Let me clarify this...  The example above came from BookLearningCocoa. Some of the examples don't quite work with versions of Mac OS X prior to 10.2.  Actually, it has NOTHING to do with MAC OS X 10.2 itself, but the version of gcc that probject builder uses.  The latest version of gcc does allow you to declare additional objects and variables after calling functions. 

I'm about 2/3 of the way through this book and I have gotten everything to compile and run (except the example that uses the address book, of course) on Mac OS X 10.1.  You just sometimes have to rearrange some of the code in the methods to make sure that all of your objects and variables are declared first BEFORE you call functions and methods in code.

ONE more snag I've come accross, there is an exercise where you create a delegate to popup an alert on whether or not to terminate an application.  The book shows (NSTerminationReply)applicationShouldTerminate: when instead it needs to be (NSApplicationTerminationReply) for Mac OS X 10.1.  Maybe apple changed this enum from 10.1 to 10.2, I don't know because I don't have access to a 10.2 install (yet -- gonna upgrade in about a month or so...)

I have to recommend this book to anyone starting out with Cocoa programming.  It starts off with excellent examples of using NSStrings in various ways and then moves on to more complex topics.  This book even mentions this web site on page 341.  Now that's cool!
----

----

**The compiler absolutely must be informed about every type before you use it**

I am very new but interested in Obj-C and Cocoa. Since I've developed
applications in Java before, I'm still getting used to the pointers and method calls [object method:parameter].

In a small tool I've written/translated from Java today, the gcc compiler gives me a parse error whenever my own class LCVertex is
mentioned. It doesn't tell me a error in the class nor its header.

I could post a excerpt from the code here if it could help, but
perhaps anybody can tell me something about common faults which
provoke this "parse error".

----

In the file that references LCVertex, did you #import "LCVertex.h"?

----

I've imported it in the main file but not in the corresponding header file. (The error appears there as well.)

----

The compiler absolutely has to know about every type you use before you use it. This means that you have to either #import your header in the .h as well (not recommended unless you're subclassing it), or you have to tell the compiler that LCVertex is a class by typing     @class LCVertex; somewhere above the part where you use it.

----

Thank you for your help. I'll test the     @class command immediately.

----

**One more time: The compiler absolutely, positively, definitely must be informed about every type before you use it**

When I try to compile this piece of art it gives:
cdda2wav.m:7: error: parse error before '=' token

I realise this probably is a very stupid question, but I am just learning!

    
#import "cdda2wav.h"

@implementation cdda2wav

- (IBAction)startRip:(id)sender
{
    cdda2wav = [[NSTask alloc] init];
    
    [cdda2wav setCurrentDirectoryPath:NSHomeDirectory()];
    
    [cdda2wav setLaunchPath:@"/sw/bin/cdda2wav"];

    [cdda2wav setArguments:[NSArray arrayWithObjects:@"-paranoia","-B","-O","aiff","cddb=1","dev=IODVDServices",nil]];

    [cdda2wav launch];
}

@end


----

    
[cdda2wav setArguments:[NSArray arrayWithObjects:@"-paranoia","-B","-O","aiff","cddb=1","dev=IODVDServices",nil]];


should be

    
[cdda2wav setArguments:[NSArray arrayWithObjects:@"-paranoia",@"-B",@"-O",@"aiff",@"cddb=1",@"dev=IODVDServices",nil]];


You need to add the @ before each NSString you're creating.

----

Thanks for looking at it, but you found another error than the one I meant, here:

(*Ed.: By golly, it probably is a declaration omission!*)

    
#import "cdda2wav.h"

@implementation cdda2wav

- (IBAction)startRip:(id)sender
{
cut..
    cdda2wav = [[NSTask alloc] init]; <-- HERE
cut..
}


----

Just wondering, you are releasing your task when it is finished right?

I don't see anything wrong with the code you posted, can you post the header file also?

-----------------------

Unless your interface for cdda2wav looks something like this:

    
@interface cdda2wav : NSObject
{
    NSTask    *cdda2wav;
}

- (IBAction)startRip: (id)sender;


then the error you are getting could be because you haven't created the variable cdda2wav yet. Also, the variable has the same name as a class. That's a no-no. The compiler doesn't know if you are talking about a variable, or the class. I'd change the name of the variable, and make sure you declare it somewhere. -- QuentinHill

-----------------------------

Don't know if you are going to be serious about Cocoa programming, but if you are you should really look into the concepts of naming convention for Cocoa.

http://developer.apple.com/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingBasics.html

--zootbobbalu

----

**And, of course, repetition of a useful idea never hurts. Once more into the breach, dear friends.**

This is basic as hell, but I still can't understand the logic behind, even though I've been doing Cocoa for some time now.
Here's what's happening, very brief and generic :

    
- (void)someFunction
{
	NSObject *myObject = [[NSObject alloc] init];
	[myObject doSomething];
	...
	// Until now, myObject has a retain count of 1.
}

- (void)someOtherFunction
{
	[myObject doSomethingElse];		// This line causes compiler error because
					// "myObject" is not defined
}


----

You're declaring myObject as a variable that's local to someFunction. Like any C variable created inside a function, the reference is lost outside of the function. Compare this to the following C code:
    
void functionA ()
{
	// This function is called first.
	int i = 0;
	printf("%i",i);
}
void functionB ()
{
	// This function is called later.
	printf("Again: %i", i);
}

Why would you expect to be able to access     i? It's not in scope at all. With Cocoa objects, it's even worse. After you leave     someFunction, the reference to     myObject is lost, so you can *never* release it! This lost memory is referred to as "garbage", and should be avoided at all costs. If you really wanted myObject to be accessed in both methods, add it to your class as an instance variable, or put a global declaration to it in the     .m file (making it a global, but only for this one file).

----

**Repetition is good for the soul**

When I put in code (directly cut & pasted from the documentation mind you!) it will not work - it simply gives a parse error. The code being pasted into my MyDocument.m file:

    
- (void)webView:(WebView *)sender didReceiveTitle:(NSString *)title forFrame:(WebFrame *)frame
{

    // Report feedback only for the main frame.

    if (frame == [sender mainFrame]){

        sender window] setTitle:title];

    }
}


The error it gives is **error: parse error before [[WebView**. Strangely enough, this code worked perfectly for me in another project...

----

Did you #import <WebKit/WebKit.h> at the top of your file?


----

**Help with weird switch case issue**

Hi. I am not sure whether this is the right place to ask this. But I am having a weird problem where if I declare a variable in the first line of a case block, the compiler generates an error: "error: syntax error before '<type>' " where <type> is the type of the variable declaration. However, if I insert any arbitrary statement, even one that is obviously useless, before the declaration, then it compiles and runs correctly. This baffles me. I am running Xcode 3.1.2 on Mac OS X 10.5.6 by the way. Thank you very much

    
// does not compile:
switch (x) {
    case 0:
        int i; // error: syntax error before 'int'
        NSLog(@"%d", i);
}


// compiles correctly:
switch (x) {
    case 0:
        42; // completely useless statement
        int i;
        NSLog(@"%d", i);
}


----
C syntax requires that a label (that's what     case 0: is) be followed by a statement. A declaration (    int i;) is not a statement, it's a separate kind of thing. C99 allows placing declarations after statements, but it does *not* make them equivalent. Therefore, following a label with a declaration is illegal syntax.

The simple workaround is to simply put the contents of your case into a block:

    
switch (x) {
    case 0: {
        int i;
        ...
        break;
    }
}


The block is legal to follow a label, and the declaration is legal within the block. Be sure to end your case with a     break as you normally would; the use of a block does not eliminate the need for that.

----
This page (and others like it) is exactly why all the experienced developers have stopped contributing to or maintaining this wiki. Too many people ignore the community rules and etiquette, use the wiki in MailingListMode, and create so many pages that amount to noise (through which you must search ... and search ... and search) when they wind down.

----
You know, I really don't think that MailingListMode is the menace that it's made out to be. The PortlandPatternRepository (THE original Wiki) has a ton of conversational-style pages and it gets by just fine. -- MikeAsh

