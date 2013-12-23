---
layout: page
title: DoubleNSWindowWhereOneIsRequired
---



 
* Following the suggestion that a subclass be created for each of NSDocument and NSWindowController, I have created the following code for PNEDocument.m, which is the File's Owner:

    

#import "PNEDocument.h" 
#import "PNEWindowController.h"

@implementation PNEDocument

- (id)init
{
	printf("Executing init in PNEDocument.\n");
    self = [super init];
    if (self) 
	{

    }
    return self;
}

- (NSString *)windowNibName
{
    return @"PNEWindowController";
}

- (void)makeWindowControllers
{
	printf("In makeWindowControllers.\n");
	[super makeWindowControllers];
	PNEWindowController * ctl = [[PNEWindowController alloc] 
								initWithWindowNibName:[self windowNibName]];
	[ctl autorelease];
	[self addWindowController:ctl];
}

- (void)windowControllerDidLoadNib:(NSWindowController *) aController
{
	printf("Executing windowControllerDidLoadNib.\n");
    [super windowControllerDidLoadNib:aController];

}



and the following code for PNEWindowController.m

    

#import "PNEWindowController.h"
#import "PNEDocument.h"

@implementation PNEWindowController

- (id)init
{
	self = [super initWithWindowNibName:@"PNEWindowController"];
	printf("In init for PNEWindowController.\n");
	return self;
}

- (void)windowDidLoad
{
	[super windowDidLoad];
	
	printf("In windowDidLoad.\n");

}



Each of the methods with a "printf" command are executed.  Problem is, that two windows appear where only one is required or desired.  Thank you in advance for any suggestions. 
--NB--



----

NSDocument automatically inits the window controller if windowNibName is implemented.It happens when you call [super makeWindowController]. Remove the [super makeindowController] part and it will work fine.

----

Thank you for your kind response.  Originally, the [super makeWindowController] was not included in the makeWindowController method.  At that time, I had the problem that the "windowControllerDidLoadNib" method was not executed.  After searching the Internet, I found that someone else had the same problem.  The suggestion to that person was to include the call to [super makeWindowController] as a fix to the "windowControllerDidLoadNib" method not being called.  After including [super makeWindowController], the "windowControllerDidLoadNib" method was properly called, but two windows were opened instead of one.  I need the "windowControllerDidLoadNib" method to execute as it is designed to set up some default text in the NSTextView, which is inside of the window.  I need the "windowDIdLoad" method to function as it is designed to resize the window and the NSTextView object contained in the window.  According to the books that I have read, both of these functions should not be performed in the NSDocument subclass, but should be done in separate subclasses; one for the NSDocument and one for the NSWindowController.  Any suggestions?

----

From cocoa documentation:
    
Typically an NSDocument subclass overrides windowNibName or makeWindowControllers, but not both. If windowNibName is overridden, the default implementation of makeWindowControllers will load the named nib file, making the NSDocument object the nib file's owner. In that case, you can override windowControllerDidLoadNib: and do custom processing after the nib file is loaded.

Do you really need to add another window controller yourself ([self addWindowController:ctl]) ?

----

Thank you for your very thoughtful response.  I removed the "makeWindowControllers" method from PNEDocument.m and nothing in PNEWindowController.m was executed except the "init" method.  When I put the "makeWindowControllers" method back into PNEDocument.m then the "windowControllerDidLoadNib" method was not executed.  I seem to be in a frustrating, and very tight, logical circle:  it is either not be able to have a functioning NSDocument subclass or not be able to have a functioning NSWindowController subclass.  Obviously, I am missing something but I cannot figure out what it is.  Again, I look forward to further discussions on this topic as it is critical to moving my project forward.

----

You need to set the current document as the nib file's owner as follows:

    
PNEWindowController * ctl = [[PNEWindowController alloc] initWithWindowNibName:[self windowNibName] owner:self];

