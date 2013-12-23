---
layout: page
title: ProperWayToUseNSToolbarInDocumentApps
---



My task today was to learn the basics of NSToolbar and succeed in implementing a toolbar in the document window of a Document-based application. I studied a variety of sources including several open source applications. From that I came up with two different implementations. I am curious if one is/may be preferred and the reason(s) why. Please excuse the verbose details as I want to make sure that even the most inexperienced will be able to use these methods. All of my explanations use the PB and the IB as I haven't moved to XCode.

Granted these are useless toolbars....but they are toolbars nonetheless and more functionality can be added.

**Implementation within an NSDocument subclass**

1) Open the MyDocument.nib of a new Cocoa Document based application in the IB. Create a new outlet named "myWindow" and connect this outlet to the Window. Save and close.

2) Edit MyDocument.h to be:
    
#import <Cocoa/Cocoa.h>

@interface MyDocument : NSDocument
{
    IBOutlet id myWindow;
}

- (void)setupToolbar;
- (NSToolbarItem *)toolbar:(NSToolbar *)toolbar
     itemForItemIdentifier:(NSString *)itemIdentifier
 willBeInsertedIntoToolbar:(BOOL)flag;
- (NSArray *)toolbarAllowedItemIdentifiers:(NSToolbar *)toolbar;
- (NSArray *)toolbarDefaultItemIdentifiers:(NSToolbar *)toolbar;

@end


3) Edit MyDocument.m to be:
    
#import "MyDocument.h"

@implementation MyDocument

- (id)init
{
    self = [super init];
    if (self) {

    }
    return self;
}

- (NSString *)windowNibName
{
    return @"MyDocument";
}

- (void)windowControllerDidLoadNib:(NSWindowController *) aController
{
    [super windowControllerDidLoadNib:aController];
    [self setupToolbar];
}

- (NSData *)dataRepresentationOfType:(NSString *)aType
{
    return nil;
}

- (BOOL)loadDataRepresentation:(NSData *)data ofType:(NSString *)aType
{
    return YES;
}

- (void)setupToolbar
{
    NSToolbar *toolbar = [[NSToolbar alloc] initWithIdentifier:@"myToolbar"];
    [toolbar setAllowsUserCustomization: YES];
    [toolbar setAutosavesConfiguration: YES];
    [toolbar setDelegate: self];
    [myWindow setToolbar:[toolbar autorelease]];
}

- (NSToolbarItem *)toolbar:(NSToolbar *)toolbar itemForItemIdentifier:(NSString *)itemIdentifier willBeInsertedIntoToolbar:(BOOL)flag
{
    NSToolbarItem *item = [[NSToolbarItem alloc] initWithItemIdentifier:itemIdentifier];
    return [item autorelease];
}

- (NSArray *)toolbarAllowedItemIdentifiers:(NSToolbar *)toolbar
{
    return [NSArray arrayWithObjects:NSToolbarSeparatorItemIdentifier,
                                     NSToolbarSpaceItemIdentifier,
                                     NSToolbarFlexibleSpaceItemIdentifier,
                                     NSToolbarCustomizeToolbarItemIdentifier, nil];
}

- (NSArray *)toolbarDefaultItemIdentifiers:(NSToolbar *)toolbar
{
    return [NSArray arrayWithObjects:NSToolbarFlexibleSpaceItemIdentifier,
                                     NSToolbarCustomizeToolbarItemIdentifier, nil];
}

@end


4) Build and run.


**Implementation within an NSWindowController subclass**

1) In a different new project....

2) Create an NSWindowController subclass in the PB, including the header file, called MyWindowController.

3) Edit MyDocument.h to be:
    
#import <Cocoa/Cocoa.h>
@class MyWindowController;


@interface MyDocument : NSDocument
{
    MyWindowController			*windowController;
}

@end


4) Edit MyDocument.m to be:
    
#import "MyDocument.h"
#import "MyWindowController.h"


@implementation MyDocument

- (id)init
{
    self = [super init];
    if (self) {
    
    }
    return self;
}

- (void)makeWindowControllers
{
    windowController = [[MyWindowController alloc] initWithWindowNibName:@"MyDocument"];
    [self addWindowController:windowController];
}

- (void)windowControllerDidLoadNib:(NSWindowController *) aController
{
    [super windowControllerDidLoadNib:aController];
}

- (NSData *)dataRepresentationOfType:(NSString *)aType
{
    return nil;
}

- (BOOL)loadDataRepresentation:(NSData *)data ofType:(NSString *)aType
{
    return YES;
}

@end


5) Edit MyWindowController.h to be:
    
#import <AppKit/AppKit.h>


@interface MyWindowController : NSWindowController
{
    NSToolbar                   *myToolbar;
}

- (NSToolbarItem *)toolbar:(NSToolbar *)toolbar
     itemForItemIdentifier:(NSString *)itemIdentifier
 willBeInsertedIntoToolbar:(BOOL)flag;
- (NSArray *)toolbarAllowedItemIdentifiers:(NSToolbar *)toolbar;
- (NSArray *)toolbarDefaultItemIdentifiers:(NSToolbar *)toolbar;

@end


6) Edit MyWindowController.m to be:
    
#import "MyWindowController.h"


@implementation MyWindowController

- (id) initWithWindowNibName:(NSString *) windowNibName
{
    self = [super initWithWindowNibName:windowNibName];
    myToolbar = [[NSToolbar alloc] initWithIdentifier:@"MyToolbar"];
    [myToolbar setAllowsUserCustomization:YES];
    [myToolbar setAutosavesConfiguration:YES];
    [myToolbar setDelegate:self];
    super window] setToolbar:myToolbar];
    return self;
}

- ([[NSToolbarItem *)toolbar:(NSToolbar *)toolbar
     itemForItemIdentifier:(NSString *)itemIdentifier
 willBeInsertedIntoToolbar:(BOOL)flag
{
    NSToolbarItem *item = [[NSToolbarItem alloc] initWithItemIdentifier:itemIdentifier];
    return [item autorelease];
}

- (NSArray *)toolbarAllowedItemIdentifiers:(NSToolbar *)toolbar
{
    return [NSArray arrayWithObjects:NSToolbarSeparatorItemIdentifier,
                                     NSToolbarSpaceItemIdentifier,
                                     NSToolbarFlexibleSpaceItemIdentifier,
                                     NSToolbarCustomizeToolbarItemIdentifier, nil];
}

- (NSArray *)toolbarDefaultItemIdentifiers:(NSToolbar *)toolbar
{
    return [NSArray arrayWithObjects:NSToolbarFlexibleSpaceItemIdentifier,
                                     NSToolbarCustomizeToolbarItemIdentifier, nil];
}

- (void) dealloc
{
    [myToolbar release];
    self window] setDelegate:nil];
    [super dealloc];
}

@end


7) Build and run.

Any comments/suggestions/opinions?

-[[NedO

*Just wondering why you used the window controller as the toolbar delegate. This seems to violate the principle of giving each class a single purpose.*

I don't see why not. If the class's single purpose is "manage the window", then "manage the toolbar" would fall under that.

----

...or consider making it a category on the window controller...

*No, because then **all** windows would use the same toolbar...*

only windows which are controlled by a MyWindowController

*Oops, I thought you meant a category on NSWindowController. My bad.*

