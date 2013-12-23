---
layout: page
title: ViewsNotUpdatingAfterUndo
---



I am trying to improve the undo function in my app and I'm having quite a lot of trouble. Basically a user can add/delete a page in a document and each page has several fields to it. Now I added the undo to the add/delete page a while ago with no problems, but now I'm adding it to the fields and I'm having some trouble with getting the views to update. The undo/redo works and the content is changed but none of the General/NSTextField, General/NSTextView or General/NSPopUpButton's are updating, but the General/NSTableView which shows the page titles does update.

I've been searching for hours for some method that will tell a window to update all views after an undo has taken place but I haven't found one yet that works. All of the views are hooked up using bindings and I think that's where the problem lies. Any suggestions?

----

Please see: General/HowToAskQuestions ... we will definitely need more detail, any relevant code you're using, etc.

----

Here is the code I have to handle undo. I've shortened the top few methods to save space, they just add all the fields. This code all works for the undo btw but I think that I need some code in here to get the views to refresh :

    

- (void)startObservingPage:(General/IPSNotes *)page {
	[page addObserver:self forKeyPath:@"pageTitle" options:General/NSKeyValueObservingOptionOld context:NULL];
	[page addObserver:self forKeyPath:@"mainBody" options:General/NSKeyValueObservingOptionOld context:NULL];
	....
	[page addObserver:self forKeyPath:@"musicCaption" options:General/NSKeyValueObservingOptionOld context:NULL];
}

- (void)stopObservingPage:(General/IPSNotes *)page {
	[page removeObserver:self forKeyPath:@"pageTitle"];
	[page removeObserver:self forKeyPath:@"mainBody"];
	....
	[page removeObserver:self forKeyPath:@"musicCaption"];
}

- (void)changeKeyPath:(General/NSString *)keyPath
			 ofObject:(id)obj
			  toValue:(id)newValue
{
	[obj setValue:newValue forKeyPath:keyPath];
}

- (void)observeValueForKeyPath:(General/NSString *)keyPath
					  ofObject:(id)object
						change:(General/NSDictionary *)change
					   context:(void *)context
{
	General/NSUndoManager *undo = [self undoManager];
	id oldValue = [change objectForKey:General/NSKeyValueChangeOldKey];
	General/undo prepareWithInvocationTarget:self] changeKeyPath:keyPath ofObject:object toValue:oldValue];
	[undo setActionName:@"Edit"];
}



I've basically taken it out of Hillegass's book. It handles the undo just fine but it won't update the fields. Just to make it clear what is working and what isn't here's my interface. The [[NSTableView on the left updates just fine but the fields on the right don't. For example, when undoing a change to the Page Title it updates it in the table view but not in the General/NSTextField. All I need help with is updating those views after an undo.

http://www.mcubedsw.com/software/screenshots/iwritermac1.png

----

Can you describe how you've set up your bindings? As well as any General/NSObjectController/General/NSArrayController objects you have set up?

I'm assuming that your document subclass contains an array of pages, and you have an General/NSArrayController whose contentArray is bound to the array of pages. And the text field, etc on the right are bound to keys of the General/NSArrayController's selection?

----

That's basically it, except I leave the array inside the General/NSArrayController rather than having it bound to an array in the document subclass. All the fields on the right are bound to the keys in the controller which contains the page objects (General/IPSNotes). I do have an General/NSObjectController in the nib file but that doesn't do anything relating to the pages

----

I've managed to fix it. I just had to put the contentArray of the General/NSArrayController into the document subclass and the interface now updates
