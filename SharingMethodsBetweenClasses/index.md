---
layout: page
title: SharingMethodsBetweenClasses
---



I need to use a method from my M**'yDocument.h/.m files
in another file - lets just call this file Example.h/.m. Normally to do this, I'd just do a #import "M**'yDocument.h", right? Well that doesn't seem to work here - when I try to use
a method from M**'yDocument in Example it just reports the method as undefined. What's wrong here? Am I missing something?

Thanks,
-JohnWells

----

So, which method is that? How did you declare it in M**'yDocument.h?

The above report is terribly sparse on details, so I am loathe to sign on to the notion of "#import not working".
You might, ummm, try checking your typography where the reference to the "undefined" method appears.

Or check your syntax in the way you have declared and/or defined the method in the M**'yDocument class
(for example, did you specify all the arguments (correctly)? etc. etc. blah blah)

see also AnatomyOfADotHFile and HowToDeclareAVariable

note how these discuss the matter of whether and when to import .h files in other header files
and the use of @class in forward declarations

In a routine circumstances, importing your headers is sufficient, so something may very well be peculiar in your source code.

----

What I'm trying to do is tell M**'yDocument to open a new browser window using the URL obtained through clicking on a link in an external app. This is the actual code I'm using:

    
// file URLHandlerCommand.m

#import "URLHandlerCommand.h"
#import "MyDocument.h"
#import "WebKit/WebView.h"

@implementation URLHandlerCommand

- (id)performDefaultImplementation {
    NSString *urlString = [self directParameter];
	
	//NSLog(@"url = %@", urlString);
	webView mainFrame] loadRequest:
   [[[NSURLRequest requestWithURL:[NSURL URLWithString:urlString stringValue] stringByAddingPercentEscapesUsingEncoding:4]
   ];

    return nil;
}

@end


And here is MyDocument.h:

    
/* MyDocument */

#import <Cocoa/Cocoa.h>

@interface MyDocument : NSDocument
{
    IBOutlet id backForwardView;
    IBOutlet id refreshView;
    IBOutlet id stopView;
    IBOutlet id urlView;
	IBOutlet id webView;
	IBOutlet id mainWindow;
	IBOutlet id backForwardButton;
	IBOutlet id googleView;
	IBOutlet id spinnerView;
	IBOutlet id urlField;
	IBOutlet id statusText;
}
- (IBAction)bfClicked:(id)sender;
- (IBAction)refreshView:(id)sender;
- (IBAction)stopClicked:(id)sender;
- (IBAction)goGoogle:(id)sender;
- (IBAction)goURL:(id)sender;
- (id)webView;
@end


loadRequest: and the like are of course handled by WebKit. The error reported is "'webView'" undeclared", so I suppose that technically one of the controls is not being shared.
-JohnWells

----

Right. OK. Technically, nonsense. You cannot send a message to an object
(well, yes, you *can* send a message to nil with no ill effects)
without getting a reference to it. In other words without an object *variable* in your other class.
In this case you have **NOT** created a webKit object in
your URL-H**'andlerCommand class yet just by referring to the one
that exists in M**'yDocument. It doesn't exist in your other class until you get a reference to it.

This goes to the heart of what an *instance* of a class is. Alternatively, you need an outlet in the other class that
points to the instance of the class that exists in M**'yDocument.

 It seems you have an accessor method that *returns* a WebView object from M**'yDocument.
But in URL-H**'andlerCommand you
are seeking to invoke a *method of that web view* without first obtaining a reference to it. Instead of doing that

why don't you try       [...[ [ myDocument webView ] mainFrame ] loadRequest...]

When SharingMethodsBetweenClasses A**'ccessorsAreYourFriends   :-)

