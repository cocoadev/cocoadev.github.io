---
layout: page
title: HowToAutosaveWindowLocationAndSize
---



I looked around on CocoaDev and found the page on NSWindowFrameAutosizing, and found this suggestion:

----
To implement per-document window frame autosizing in an NSDocument based app (as recommended by jcr from apple), just put this in awakeFromNib of your window controller:

    
// window frame autosave
[self setShouldCascadeWindows: NO];
[self setWindowFrameAutosaveName: self document] fileName;


----

I tried the above code without success. I had to tweak this code to get it to work for a blank file, because fileName would come back as (null), and the window wouldn't open. Here's the modified code:

    
// window frame autosave
[self setShouldCascadeWindows: NO];
NSString *fileName = self document] fileName];
if (fileName != NULL) [self setWindowFrameAutosaveName: fileName];


It still wouldn't save the location and size, though. Do I need to set up preferences somewhere, or is this code that's no longer valid under 10.4, or something else entirely?

Thanks!

----
The modified code should probably be using     nil instead of     NULL (just a style note), but I would guess the larger problem may be that you didn't change the autosave name when the file is saved (thus leaving the window with an empty autosave name). Does opening an existing document save the location/size? --[[JediKnil

----
Ensure that you DONT have an autosave name for the NSWindow in your Nib, as its existence would interfere with your programatic autosave in your NSWindowController.

----
Thanks for the suggestions. Still no luck, though. I've checked to make sure that there's no autosave name in the nib, and added a few lines of code to set the autosave name when the file is being saved, and the location and size don't save, even for previously named & saved files. 

Any other suggestions? Is there some example code in an application that does this that I can look at for hints?

Thanks again!  NKH

----
Try using     -setFrameAutosaveName: instead?  http://www.cocoabuilder.com/archive/message/cocoa/2004/3/16/101830

----
I tried     -setFrameAutosaveName:, and got the app to save and restore the window size correctly, after fixing some new problems. First, the run log started to report "[<AppController 0x146082e0> valueForUndefinedKey:]: this class is not key value coding-compliant for the key bugs." when trying to save. The second was that the program sometimes crashed when quiting. I solved the first by adding key-value coding to everything that the log complained about (I'd fix one, then another would pop up), and fixing that apparently fixed the crashing as well.  

Here's the code to try to do the autosaving, from     MyDocument.m:

    
- (void)windowControllerDidLoadNib:(NSWindowController *) aController {
    [super windowControllerDidLoadNib:aController];
	[bugController addObserver:self forKeyPath:@"selectionIndex" options:NSKeyValueObservingOptionNew context:NULL];
	NSLog(@"Selected row is %d\n", [bugController selectionIndex]);
	NSLog(@"Mydocument windowControllerDidLoadNib\n");
	NSString *fileName = [self fileName];
	if (fileName != nil) {
		NSLog(@"File's name is %@\n", fileName);
		aController window] setFrameAutosaveName:fileName];
	} else {
		[[NSLog(@"No file name\n");
	}
}

- (NSData *)dataOfType:(NSString *)typeName error:(NSError **)outError {
	// End editing
	[bugController commitEditing];
	
	NSString *fileName = [self fileName];
	appController window] setFrameAutosaveName:fileName];
	// Create an [[NSData object from the bugs array
	return [NSKeyedArchiver archivedDataWithRootObject:bugs];
}


Correcting the key-value coding compliance required adding key-value coding compliance to several objects in the document file (that weren't really part of the document as I saw it), and then having the     appController's key-value coding grab the values from its document.

