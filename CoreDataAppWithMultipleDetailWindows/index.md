---
layout: page
title: CoreDataAppWithMultipleDetailWindows
---

I have an app created with CoreData that presents two entities (classroom and student), one-to-many and one-to-one, respectively.

A single window presents a list with classrooms and, instead of using the traditional model of showing all the editing controls on the same window, I want to use separate windows - ONE FOR EACH ITEM I DOUBLE-CLICK.

That would be like the Mail.app interface, where you have individual mail messages and a master list view. How do I implement that?

I didn't want to start with a document-based app template because I don't want each document to be individual, I want the information to be centralized in the way Mail and iTunes work.

One of my approaches was to create a subclass of NSWindowController and create a method at the application delegate to load a window from an external NIB when the user double-clicks the item on the list. But I'm having some trouble with this approach (haven't been able to use bindings for that, and the window loads but does not become key no matter what method I've been using).

Any idea of what would be the best approach here?

Thanks a lot!

----

In our software, we call the windows to which you are referring "edit cards". And the idea behind your approach is the usual solution: create a nib with a window in it, laid out like you'd want. Create a window controller and make it the File's Owner of the nib. Then -initWithWindowNibName:. And set the window in the nib to be released on close, probably.

Your window controller is going to need to be the entry point for all the key paths of the controls in your window.

I wouldn't have used the app delegate to create the window controller, unless it's acting as the delegate for the table view. I'd have thought you'd make a dedicated window controller for the main window, but maybe your app is small enough that it's not necessary.

So, where exactly did things start to break down when you tried it?

Finally, in future, consider posting such question in the Forums (linked above).

----

Hey, thanks for the reply, and sorry for the delay in answering. Definitively will use the forums in the future.

That's exactly what I did. I have the WindowController class defined in Xcode and in IB, the WindowController set as File's Owner, and the following code from the app's delegate (using a delegate for now to try to keep it simple):

    
- (IBAction)loadDetailWindow:sender {
	theWindowController = [[DetailWindowController alloc] initWithWindowNibName:@"DetailWindow"];
	NSWindow *theWindow = [theWindowController window];
	[theWindow makeKeyAndOrderFront:self];
	if ([theWindow isKeyWindow]) {
		NSLog(@"Yep");
	} else {
		NSLog(@"Nope");
	}
}


No matter how I rewrite the code (ex: , tried using NSApp activateIgnoringOtherApps:YES , or loading the nib at the WindowController's constructor), the window does not become key (the code above will log "Nope").

Any clue?

----

Found the answer to my own question! I forgot one last detail. On IB, the File Owner's window outlet has to be connected to the actual window. That will cause the windows to become key when launched.

