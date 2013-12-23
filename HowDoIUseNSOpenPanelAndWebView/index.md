---
layout: page
title: HowDoIUseNSOpenPanelAndWebView
---

Hello. I've taken the task of making a Cocoa WYSIWYG HTML editor for websites, kinda like Nvu but in Cocoa. I tried it in Java, a language I am fluent in, but I hit a  few road blocks. I know very little Objective-C, but enough to get myself by. What I have tried with no success 3 times was to use the WebView control to edit a page. I want to know the following:


1) How do I get the WebView control to make itself editable the moment the window is opened?

2) If I can't do that, how do I properly use NSOpenPanel? I tried a NSArray with a weird error; and an NSMutableArray didn't work.

3) How do I make those messageboxes that come up out from the titlebar? I will need to know what happens during debugging, and I have used messageboxes back in Windows to tell me.  


Thanks, Pietro Gagliardi <http://web.mac.com/pietro10/>

----
1) Use     awakeFromNib

2) See HowToAskQuestions

3) They're called "sheets", but this is a terrible way to debug. Beyond terrible: horrible, unspeakably bad. Use NSLog and the Xcode debugger.

----
OK, I'll look into NSLog. The programming language I use in Windows doesn't have debugger logs you could write to (just the error log if something happened, but that was a text file that was created at crash time). Thanks for pointing me to NSLog. I'd still like to know how to use the default sheets in my programs (Are you sure?, etc.).

...

I am having trouble again. How do I use awakeFromNib?

----
Maybe you don't actually know "enough to get myself by". It may be time to look into some CocoaTutorials and CocoaBooks.

----
I meant I knew enough Objective-C to get by. I have already tried some of the tutorials; maybe I'll try more soon. If I still need help I wil reply here.

----
I need help now. Here is my code:

Controller.m
    
 #import "Controller.h"
 #import <AppKit/NSNibLoading.h>
 #import <AppKit/NSPanel.h>
 
 @implementation Controller
 - (void)awakeFromNib
 {
 	NSOpenPanel *opener;
 	int i;
 
 	NSLog(@"About to select file");
 	opener = [NSOpenPanel openPanel];
 fileSelect:
 	[opener setAllowsMultipleSelection:NO];
 	[opener setCanChooseDirectories:NO];
 	i = [opener runModalForTypes:nil]; // any type; real types being added soon!
 	if (i != NSOKButton) {
 		NSLog(@"Going back, because you MUST choose a file.");
 //		NSAlert alertWithMessageText:@"You must choose a file." defaultButton:nil alternateButtonTitle:nil otherButtonTitle:nil informativeTextWithFormat:@"Please choose a file to continue."] runModal];
 		goto fileSelect;
 	}
 	NSLog(@"File chosen: %@", [[opener filenames] objectAtIndex:0]);
 	NSLog(@"Opening...");
 	/**** FATAL BUG HERE - GOTTA FIX !!! ****/
 	[webView takeStringURLFrom:[[opener filenames] objectAtIndex:0;
 	/**** THIS IS THE ONLY LINE WITH BUG ****/
 	NSLog(@"About to set editable");
 	[webView setEditable:YES];
 	if ([webView isEditable] == NO)
 		NSLog(@"!! EDITABLE FAILED!!!");
 	else
 		NSLog(@"Editable Set!");
 }
 @end


What am I doing wrong on the line with the comments around it?

----
WebView only opens string URLs (whether they be local or remote URLs) when they are properly formatted with the protocol and everything (i.e. http://somewebsitenamehere.com or file:///Users/myhomedir/Sites/index.html)

----
RTFM: "This method sets the receiver's current location to the value obtained by sending a stringValue message to sender." NSString does not respond to     stringValue.

----
That helps a lot. So what does reply to stringValue? NSURL?

----
NSCells respond to stringValue, which means most controls do, such as NSTextField. Just look in Apple's docs to see what NSURL responds to. For simple strings, there's     absoluteString and     relativeString, along with stuff for each component of a URL. Every class in Cocoa has a documentation page on Apple's site which lists it's methods and information on how to use each one. There is also more in-depth info on many of the classes if you search for them on this site. --LoganCollins

----
But I need to get the value to open out of a NSArray, not a NSCell. The NSArray is     [NSOpenPanel filenames]. Is there another method in WebView to do this?

----
WebView has a class documentation on Apple's site too. Here it is: http://developer.apple.com/documentation/Cocoa/Reference/WebKit/Classes/WebView_Class/Reference/Reference.html. To load a page from disk or URL, you have to do it using the WebView's primary WebFrame, which you get with     [myWebView mainFrame]. Then, just send one of the WebFrame load request methods to it and you're on your way! --LoganCollins

----
THANK YOU A LOT! My problem has been resolved. Here is the final code. I might put it in WebView. I shall now commence development of this program, which I shall call MacView.

    
 #import "Controller.h"
 #import <AppKit/NSNibLoading.h>
 #import <AppKit/NSPanel.h>
 
 @implementation Controller
 - (void)awakeFromNib
 {
 	NSOpenPanel *opener;
 	NSURLRequest *rq;
 	int i;
 
 	NSLog(@"About to select file");
 	opener = [NSOpenPanel openPanel];
 fileSelect:
 	[opener setAllowsMultipleSelection:NO];
 	[opener setCanChooseDirectories:NO];
 	i = [opener runModalForTypes:nil]; // any type; real types being added soon!
 	if (i != NSOKButton) {
 		NSLog(@"Going back, because you MUST choose a file.");
 //		NSAlert alertWithMessageText:@"You must choose a file." defaultButton:nil alternateButtonTitle:nil otherButtonTitle:nil informativeTextWithFormat:@"Please choose a file to continue."] runModal];
 		goto fileSelect;
 	}
 	NSLog(@"File chosen: %@", [[opener filenames] objectAtIndex:0]);
 	NSLog(@"Opening...");
 	rq = [NSURLRequest alloc];
 	[rq initWithURL:[[opener URLs] objectAtIndex:0;
 	webView mainFrame] loadRequest:rq];
 	NSLog(@"Laoded.");
 	NSLog(@"About to set editable");
 	[webView setEditable:YES];
 	if ([webView isEditable] == NO) {
 		NSLog(@"!! EDITABLE FAILED!!!");
 		return;
 	}
 	NSLog(@"Editable Set!");
 }
 @end


The only issue I have left is with the [[NSAlert, but I will solve that late in development.

----
One more thing...watch your memory allocation. You have memory leaks on your URL request. Remember to release anything you allocate or use autorelease. --LoganCollins

----
OK, here is the now memory-friendly (but still functional) code:

    
 #import "Controller.h"
 #import <AppKit/NSNibLoading.h>
 #import <AppKit/NSPanel.h>
 
 @implementation Controller
 - (void)awakeFromNib
 {
 	NSOpenPanel *opener;
 	NSURLRequest *rq;
 	int i;
 
 	NSLog(@"About to select file");
 	opener = [NSOpenPanel openPanel];
 fileSelect:
 	[opener setAllowsMultipleSelection:NO];
 	[opener setCanChooseDirectories:NO];
 	i = [opener runModalForTypes:nil]; // any type; real types being added soon!
 	if (i != NSOKButton) {
 		NSLog(@"Going back, because you MUST choose a file.");
 //		NSAlert alertWithMessageText:@"You must choose a file." defaultButton:nil alternateButtonTitle:nil otherButtonTitle:nil informativeTextWithFormat:@"Please choose a file to continue."] runModal];
 		goto fileSelect;
 	}
 	NSLog(@"File chosen: %@", [[opener filenames] objectAtIndex:0]);
 	NSLog(@"Opening...");
 	rq = [NSURLRequest alloc];
 	[rq initWithURL:[[opener URLs] objectAtIndex:0;
 	webView mainFrame] loadRequest:rq];
 	NSLog(@"Loaded.");
 	NSLog(@"About to set editable");
 	[webView setEditable:YES];
 	if ([webView isEditable] == NO) {
 		NSLog(@"!! EDITABLE FAILED!!!");
 		[rq release];
 		return;
 	}
 	NSLog(@"Editable Set!");
 	[rq release];
 }
 @end


----
It is mandatory to nest alloc/init. You cannot call them separately unless you assign the result of init. Example:

    
 foo = [[Bar alloc] init]; // legal
 
 foo = [Bar alloc];
 foo = [foo init]; // legal but strange
 
 foo = [Bar alloc];
 [foo init]; // illegal, this can cause your code to explode in mysterious ways


----
OK, I changed the following line:

    
 // old
 [rq initWithURL:[[opener URLs] objectAtIndex:0;
 
 // new
 rq = [rq initWithURL:opener URLs] objectAtIndex:0;


One reason to use Java: automatic garbage collection.
One reason to force Apple to release Objective-C 2.0 now: Automatic garbage collection :-)

----
The mandatory nesting of alloc/init has nothing to do with ObjC 2.0 or garbage collection, and will continue to be required even after those are available.

