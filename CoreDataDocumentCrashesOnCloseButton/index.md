---
layout: page
title: CoreDataDocumentCrashesOnCloseButton
---


Hello. I have written a simple program using Core Data and a document-based format (choose New Project > Core Data Document based application). I thought I was done, but then I clicked the red window close button for the sole window that was open. The program crashed. I poked around in the source code for a bit and discovered that if I created a -(void) dealloc method for the MyDocument class, but did NOT call [super dealloc] (it's commented out in the version below...), the program did NOT crash. Also, if I close any window but the last one, it doesn't crash. Since I hadn't sent any retains, releases, or autoreleases to anything I thought was useful here, I don't understand what's happening. Have I done something wrong in my code? I'd appreciate any insight you might have. Additional note: just created a blank project. It does NOT crash when I click the close button on the boilerplate window. Thanks! -Dan http://goo.gl/OeSCu
The offending MyDocument class:
    
#import "MyDocument.h"
#import "MyAttribStringToNSStringTransformer.h"

@implementation MyDocument

- (id)init 
{
    self = [super init];
    if (self != nil)
	{
        // initialization code
		[[NSApplication sharedApplication] setDelegate:self];		// Allows us to receive applicationDidFinishLaunching notifications.
		
		// Register our value transformer here. This transformer allows us to use key value bindings with the NSScrollView from IB.
		id transformer = [[[MyAttribStringToNSStringTransformer alloc] init] autorelease];
		[NSValueTransformer setValueTransformer:transformer forName:@"MyAttribStringToNSStringTransformer"]; 
    }
    return self;
}

- (NSString *)windowNibName 
{
    return @"MyDocument";
}

- (void)windowControllerDidLoadNib:(NSWindowController *)windowController 
{
    [super windowControllerDidLoadNib:windowController];
    // user interface preparation code
}

-(void) applicationDidFinishLaunching:(NSNotification *)aNotification
{
	//Nothing to do here.
}

-(void)dealloc
{
//	[super dealloc];
}
@end

The Debugger output:
#0	0xfffeff20 in objc_msgSend_rtp
#1	0x93841d54 in -[NSApplication(NSWindowCache) _checkForTerminateAfterLastWindowClosed:]
#2	0x92bebc04 in __NSFireDelayedPerform
#3	0x907f2578 in __CFRunLoopDoTimer
#4	0x907deef8 in __CFRunLoopRun
#5	0x907de4ac in CFRunLoopRunSpecific
#6	0x93298b20 in RunCurrentEventLoopInMode
#7	0x932981b4 in ReceiveNextEventCommon
#8	0x93298020 in BlockUntilNextEventMatchingListInMode
#9	0x9379eae4 in _DPSNextEvent
#10	0x9379e7a8 in -[NSApplication nextEventMatchingMask:untilDate:inMode:dequeue:]
#11	0x9379acec in -[NSApplication run]
#12	0x9388b87c in NSApplicationMain
#13	0x0000dfa8 in main at main.m:13
----
OK, answered my own question. I added this to the dealloc method, and re-enabled the call to super.
    [[NSApplication sharedApplication] setDelegate:nil];
I guess the NSApp calls the (now dealloc-ed) delegate (itself) to ask if it should terminate after the final window is closed. Sloppy work on my part....

----

I've never quite seen an app delegate connected in this way. Usually your app delegate class is instantiated in Interface Builder (your MainMenu.nib) and connected via the File's Owner (NSApplication in this instance) delegate outlet. The problem you're facing simply doesn't exist when it's done that way.

Not that your way is wrong, mind you, I'm just pointing out the departure from most examples I've seen (and the way I do things myself).
----
Well, I'm still a novice here, so I guess what happened is that I realized I needed to tell some object to act as a delegate, so I looked through the documentation and found the message to arrange this. I suspect that IB probably sends similar messages behind the scenes, so to speak, but it also takes care of nullifying the connection before messages get passed along to a defunct object. Along those lines, is there a reason to prefer doing it one way or the other, beyond what is common? On a broader scale, as a novice, I would like to reinforce the good habits, but it's awfully hard to tell if I'm engaging in "good programming practices" when all I can use to judge is that the code works. Any notes on style are duly noted and appreciated. (I think I'll move this back to "discussions" for now, in case you or anyone else wants to weigh in.) -Dan

----
Typically, you have a separate object act as the application delegate.  In your case, what happens if you have multiple documents open at once?  Questions like this don't arise if you keep the "Document" portion of your document-based app separate from the "App" portion.
If your app delegate needs to talk to the document, use (MyDocument *)[[NSDocumentController sharedDocumentController] currentDocument].
----
Just so I'm clear, the problem you're alluding to is: What happens if I have multiple documents open when the app finishes launching? In other words, the "applicationDidFinishLaunching" notification (in this case) would be received several times (once for each document instance?). I can see that that might be troubling behavior in certain circumstances. Did I get that right? (Incidentally, I changed the setup so that the delegate is define in IB, and it points to a separate MyAppController object, as it should.) Thanks! -Dan
----
No, I really meant "what happens if I open a new document?"  Which one becomes the app delegate?  And then, when I close the frontmost document, how does any of my documents know to re-wire itself to the application.  It's fundamental questions like these that should ring alarm bells as you're writing your code, but can only really be recognized with a full understanding of the model.  I'd suggest reading up on delegates (what they are and how they work) a bit more, as well as the whole NSDocument framework.
----
OK, I see I need to study those things more thoroughly. Thanks!

