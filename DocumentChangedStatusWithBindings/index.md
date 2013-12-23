---
layout: page
title: DocumentChangedStatusWithBindings
---

CocoaBindings has eliminated so much code for me that I no longer have a convenient place to update my document-changed status.

App uses a table view without any fancy stuff, targeted to add and delete rows with default implementations in the array controller object
- I did not override add: and remove: in the array controller.

Search for "NSUndoManager AND cocoa AND bindings" and 50% of the hits lead you to Aaron Hillegass' chapter on the undo manager in the new edition of his book.

----

The lack of a delegate or notifications is one of the most annoying things about using bindings. You need to become an observer of all the objects in the NSArrayController. That way, whenever one of those objects changes (usually by a binding informing the NSArrayController), the controller object is notified because it is an observer. Basically, bind the contentArray binding of an NSArrayController to an array in our other controller object (MyDocument), so the NSArrayController informs us whenever it would like to add or remove an object from the array, which gives us a hook to start or stop observing that object.

----

Why not register your *document* to listen for "MyAppDataChangedNotification" or whatever? Make your NSObjectControllers and NSArrayControllers responsible for sending up a flare any time something is added or removed. If you use NSObjectControllers as a proxy for editing the objects in your array controller, you can override the editing methods to send up this flare as well.

----

All I have to do currently is get the [ arrayController arrangedObjects ] every time I need the array for something - and I can always do this via the window controller which has an outlet to the array controller.

The document status stuff is easy just by overriding the add: and remove: methods in the array controller and sending the updateChangeCount: message to the window controller. The way to get the document-changed status for editing cells directly is easy by including the controlTextDidChange: method to get notifications from an object of the table view class and setting the delegate for the table view as the array controller.

I guess the solution (if any) will be in the KVO stuff from the 2nd ed. of the Hillegass book on p. 146 ff.

I don't quite see how to forward the proper invocations to the window controller where the undo manager instance will be and then do callbacks to place on the undo stack the inverse of the respective method in the array controller.

----

In the example in BookCocoaProgOSX, the real meat of the "document" is the array of Person objects. Any real changes that the user makes in the interface affects that array of Person objects: adding an object, deleting an object, or modifying an object. In order to make those changes undoable, we must have hooks that let us run custom code when one of those three things happen. The easiest way to get those hooks is by making the NSArrayController use an array that MyDocument manages, rather than using its own array. NSArrayController will tell MyDocument to add or delete objects from its array, which gives us hooks to run some custom code before actually adding or deleting the objects. We must use key-value observing to receive automatic notifications when a Person object changes, which gives us the hooks to run custom code and make the change undoable.

I'd stay away from trying to subclass NSArrayController to handle undo -- we tried that and  Apple recommended moving in the direction of using Key-Value Observing and maintaining our own array instead. Hopefully, NSArrayController will eventually get its own undo-handling...but until then, its internals are not nearly well-documented enough to make subclassing a good idea.

----

Except for placing the relevant code in the window controller rather than in the NSDocument subclass, I have followed these well-meaning suggestions, but not to the letter (that is, by following as best I can the example in the book.) By setting the bindings for the contentArray using the array copy in the window controller for the model key path, the first thing you find is that the table view does not display any data. You can fix this by setting the property "Handles content as compound value". At this stage, you get back to having add (or insert) and delete capabilities for the document. But the undo manager is **NOT** engaged. Although the book example supposedly has achieved undo and redo capabilities at this point (refer to the section-ending paragraph on p. 146 of the book), my example does not. When it is placed in the window controller, the code for modifying the array copy is not activated when the array controller adds and removes objects. Yet the book claims that you should have undo and redo capabilities for table additions and deletions at this point.

There's always the traditional way of accessing the undo manager. To be fair, the approach of binding the window controller's copy of the table array to the array controller's content array actually updates the copy. But the methods that alert the undo manager are not automatically invoked when they are located in the window controller. (The book example puts them in the document subclass and says they should be invoked automatically when the array changes.)

----

Unfortunately, adding undo support to an app that uses an NSArrayController was not something common enough for Apple to make it easy.

In the book, the undo example illustrates an idea: don't try to add this functionality directly to NSArrayController by subclassing, because it's not designed to work that way. Instead, here's how you can use the contentArray binding of the controller to employ a custom controller object and give it the hooks needed to add things like undo support.

There's more to providing undo support than just setting the contentArray binding on the controller. You still need to add undo support "the hard way" -- e.g., how you would have done things even without an NSArrayController and bindings. Using the contentArray binding and key-value observing allows you to get the hooks needed for undo code when the user is doing most of his editing by interacting with an NSArrayController, rather than your manually-controlled widgets and action methods.

----

Completely refactoring a previously bindings-enabled app to gain undo support seems to defeat the intent of bindings and the MVC paradigm as well.

*Actually, the real problem is that NSArrayController is not key-value observing compliant on any of its interesting keys (e.g. contentArray, arrangedObjects).* --BenStiglitz

----

The fact that you don't have "manually-controlled widgets and action methods" is exactly my point. That's what bindings are replacing. But when you had manual control over everything, you had all these places where you could stick in a call to the undo manager. You still need to stick in those calls, but using a different mechanism to get the hooks you need: key-value observing. Instead of the user clicking "Add" to call your add: method, "Delete" to call your delete: method, and editing a text field to call your nameFieldChanged: method, bindings is taking care of all that.

As far as binding the array controller's contentArray binding to another array somewhere...that's exactly what the contentArray binding is for, so I don't know why it's giving you problems.

Bindings exist to connect the Model to the View, thus acting as part of the Controller layer. It fits very nicely into that role. But it's implemented to connect the Model to the View in a specific way, and more work is needed if you wish to do something different. You do that work by supplying some additional Controller layer code (in a custom object) and using key-value observing to call your code when appropriate.

----

I just implemented the suggestions from the book and it works flawlessly.  One thing to be aware of, though.  If any of your properties are collections (arrays or dictionaries) you will need an extra check.  You can check like this:

    
- (void)observeValueForKeyPath:(NSString *)keyPath
					  ofObject:(id)object
						change:(NSDictionary *)change
					   context:(void *)context {
					   
	NSUndoManager *undo = [self undoManager];
	
	// set non-collection value
	if (change objectForKey:[[NSKeyValueChangeKindKey] intValue] == NSKeyValueChangeSetting) {
		id oldValue = [change objectForKey:NSKeyValueChangeOldKey];
		//NSLog(@"oldValue for %@ %@ = %@", [object name], keyPath, oldValue);
		undo prepareWithInvocationTarget:self] changeKeyPath:keyPath
			ofObject:object
			toValue:oldValue];
		[undo setActionName:@"Edit"];
		
	// insert, remove, replace object in collection
	} else {
		// no undo for these actions for now
		[self updateChangeCount: [[NSChangeDone];
	}
}



In this example I simply update the changed status on my doc instead of providing undo support for collections.

--kinch

