---
layout: page
title: NSTableViewTriggersUpdate
---

Goal:
To have textual changes in an NSTableView trigger an update of various other NSTextFields.

I am using an NSArrayController to manage the table via bindings.

I have already tried using bindings, and they mostly work, but the update only occurs when a new row is added to the table. This is not acceptable.
I used 'valueForKeyPath:@"@sum.total" on the default arrangedObjects. Where total is a method I wrote.

Currently I am trying a delegate approach via an AppController. I have set the delegate of my NSTableView to the AppController and am trying to get the 'textDidChange:' selector to trigger the update. Unfortunately the 'textDidChange:' selector in my AppController is not being called. What have I done wrong or not done at all?

Code snippet:
    
AppController.h
------------------------
#import <Cocoa/Cocoa.h>
#import "MyDocument.h"
#import "Transaction.h"

@interface AppController : NSObject {
    IBOutlet NSTextField *balanceField;
    IBOutlet NSTableView *tableView;
    IBOutlet MyDocument *myDocument;
    IBOutlet NSButton *updateButton;
}

- (IBAction)updateBalanceField:(id)sender;

@end

AppController.m
------------------------
#import "AppController.h"

@implementation AppController

- (id)init
{
    [super init];
    return self;
}

- (IBAction)updateBalanceField:(id)sender;
{
    Transaction *t;
    NSMutableArray *trans = [myDocument transactions];
    float total = 0.0;
    NSEnumerator *e = [trans objectEnumerator];
    while(t = [e nextObject]) {
        total += [t total];
    }
    [balanceField setStringValue:[[NSNumber numberWithFloat:total] description]];
}

- (void)textDidChange:(NSNotification *)aNotification
{
    NSLog(@"textDidChange");
    //[self updateBalanceField];
}

- (void)dealloc
{
    [super dealloc];
}

@end


----Try using -controlTextDidChange: instead. -textDidChange: is sent to the delegate of a NSTextView object, which for the field editor is the text field into which you are currently typing. IIRC, -controlTextDidChange: is sent at the same time to the text field's delegate (your controller object.)

----This fixed the problem perfectly. For some reason I didn't bother to scroll to the end of NSControl to see the delegate methods. I'm accustomed to reading Java APIs where all of the summary and methods are described at the top of the page.

