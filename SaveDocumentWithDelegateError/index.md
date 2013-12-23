---
layout: page
title: SaveDocumentWithDelegateError
---

I'm having problems when I need to be sure that all my documents are saved before the program continues executing:
The example method below says it all. 
I need to be sure that all documents are saved before I continue but after sending the message
    
[[NSDocumentController sharedDocumentController]saveAllDocuments];

the program continues without waiting. Perhaps a second thread is used to actually save the documents?
How to handle this?

In my subclass of NSDocument:

    
-(void) doSomething
{
        [[NSDocumentControllersharedDocumentController]saveAllDocuments:nil];
        // now that all documents are saved and the user did not cancel saving untitled documents

        if( there are no more unsaved documents)
        {
                continue processing
                ....
        }
        ...return without continuing
}  


----

*the program continues without waiting. Perhaps a second thread is used to actually save the documents?*

It has to return, because the app is supposed to keep responding to input while the user reads the dialog(s) and decides what to do.  It probably isn't in a second thread, I'd expect it to use the runloop.  Anyway, I think you'll need to do this rather more manually.  Go through each unsaved document and tell it to save itself, maybe with     -[NSDocument saveDocumentWithDelegate:didSaveSelector:contextInfo:] if that's the kind of behavior you want (some other related methods can be used to get different behavior).  If there aren't any unsaved documents directly call a method that does your real work.  In the didSaveSelector you specify, see if all the docs have been saved, and if so call that real work method.

----

I have a NSDocument (myDocument) which displays a sheet (myDialog).
myDocument is an untitled, modified (changed) document.

    
@implementation myDocument
- (void) showDialog
{
	[[NSApplication sharedApplication] beginSheet: myDialogWindow 
				modalForWindow:[self window] modalDelegate:self 
				didEndSelector:@selector(sheetDidEnd:returnCode:contextInfo:) contextInfo:nil];
}
@end


This sheet calls saveDocumentWithDelegate

    
@implementation myDialog
-(void) aMethod
{
	// this should present the savePanel because it is an untitled document
	[myDocument saveDocumentWithDelegate:self didSaveSelector:@selector( documentIsSaved:didSave:contextInfo:) contextInfo:nil];
}

- (void) documentIsSaved:(NSDocument *)doc didSave:(BOOL)didSave contextInfo:(void*)contextInfo
{
	if( didSave ==NO)
	{
		.....// never get here 
	}
}
@end


My problem is this:
The only result I get is a beep and I can not check in my program why the document could not be saved.
If I try this with an existing document that only needs saving it works. In that case, the document is saved.
So the main problem is that the savePanel can not be used but I wonder why and most of all, how can I see that saving is failing here.
Instead of this beep I need to write some code to handle this error.

----

Have you tried ending the sheet before you call     saveDocumentWithDelegate:? If so, have you tried putting it in a delayed perform (by calling     performSelector:withArgument:afterDelay: with a delay of 0)?

----

If I end the sheet then everything is fine but the point is to keep the sheet. I know it is unusual but in this case it is acceptable.
I know there is no problem in having sheets on top of each other.
Maybe I should end the sheet and once the document is saved reopen it...
However, the question remains... why the beep without a way of catching things?
In this case I know the cause of the beep, but there may be occasions, other than the user hitting the cancel button in the savePanel dialog, where the saveDocumentWithDelegate: fales.

----

Opening a sheet when a sheet is already open may be possible now, but I believe it wasn't always, and in any case it's considered bad UI. If you really want to do it, then close the sheet, run the save, and re-open.

I'm hoping that the beep is only present in this one case. I agree that it shouldn't do that, but I think that if you test the other cases, it will be called there.

