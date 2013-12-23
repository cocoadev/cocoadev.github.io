---
layout: page
title: ReplaceSaveChangesSheet
---

(From mailing-list posts by Dan Bernstein and Mark Piccirelli)

**Q:**
In my document-based application, I want to replace the "Do you want to 
save changes..." sheet with one of my own, but to maintain the exact 
same behavior. How do I do it? (I tried overriding 
canCloseDocumentWithDelegate: to no avail).
I want my NSDocument subclass to override 
canCloseDocumentWithDelegate:shouldCloseSelector:contextInfo: in order 
to replace the "Do you want to save changes...?" alert with different 
phrasing and options (for example, see how Terminal.app or Mail.app 
behave when you try to close a dirty window).

According to the docs, the default implementation displays the alert 
sheet, and after the user makes a choice, calls the delegate's 
shouldCloseSelector with an appropriate value. My problem is, of 
course, that the method that handles the user's response to the sheet 
(the didEndSelector) doesn't know what delegate and selector were given 
to canCloseDocumentWithDelegate:.

I can find a way to stick this in the contextInfo, but that seems ugly. 
How is this supposed to be done? Better yet, how does the default 
implementation do it?

**A:**
Replacement of the "Do you want to save the changes..." alert is not one 
of the customizations of NSDocument we've anticipated, so it's no fun to 
do, but I think there is a way to do it that's not likely to break when 
run against future Mac OS X releases.

It sounds like you really should override -[NSDocument 
-canCloseDocumentWithDelegate:shouldCloseSelector:contextInfo:].  Here's 
some sample code to walk you through it.  It's very similar to what 
NSDocument itself does.  You have to override the method itself, provide 
another method that will be invoked after the alert sheet has been 
dismissed, and declare a context structure just to remember a little bit 
of stuff while the sheet's being presented.  I haven't actually compiled 
this code, so anything that looks like a typo probably is.

				
    
typedef struct {
     id delegate;
     SEL shouldCloseSelector;
     void *contextInfo;
} CanCloseAlertContext;

- (void)canCloseAlertSheet:(NSWindow *)inAlertSheet 
didEndAndReturn:(int)inReturnCode withContextInfo:(void *)inContextInfo {
     CanCloseAlertContext *canCloseAlertContext = inContextInfo;

	// The user's dismissed our "save changes?" alert sheet.  What 
happens next depends on how the dismissal was done.
     if (inReturnCode==NSAlertAlternateReturn) {

         // The document is not to be saved.  Tell the delegate of the 
original -canCloseDocumentWithDelegate:... to just continue closing.
         if (canCloseAlertContext->shouldCloseSelector) {
             void (*callback)(id, SEL, NSDocument *, BOOL, void *) = 
(void (*)(id, SEL, NSDocument *, BOOL, void *))objc_msgSend;
             (*callback)(canCloseAlertContext->delegate, 
canCloseAlertContext->shouldCloseSelector, self, YES, 
canCloseAlertContext->contextInfo);
         }

     } else if (inReturnCode==NSAlertDefaultReturn) {

		// Explicitly dismiss the alert sheet here, because others may 
be about to be presented.
		// (I don't remember if this is still necessary in 10.1, but 
NSDocument still does it, so it apparently doesn't hurt.)
         if (inAlertSheet) [inAlertSheet orderOut:self];

		// The document is to be saved.  Save the document, taking 
advantage of the fact that in this situation "can close" will be 
synonymous with "did save."
         [self saveDocumentWithDelegate:canCloseAlertContext->delegate 
didSaveSelector:canCloseAlertContext->shouldCloseSelector 
contextInfo:canCloseAlertContext->contextInfo];

     } else {

		// The closing was cancelled.  Tell the delegate of the 
original -canCloseDocumentWithDelegate:... to not close.
         if (canCloseAlertContext->shouldCloseSelector) {
             void (*callback)(id, SEL, NSDocument *, BOOL, void *) = 
(void (*)(id, SEL, NSDocument *, BOOL, void *))objc_msgSend;
             (*callback)(canCloseAlertContext->delegate, 
canCloseAlertContext->shouldCloseSelector, self, NO, 
canCloseAlertContext->contextInfo);
         }

     }

	// Free up the memory that was allocated in 
-canCloseDocumentWithDelegate:shouldCloseSelector:contextInfo:.
     free(canCloseAlertContext);

}

- (void)canCloseDocumentWithDelegate:(id)inDelegate 
shouldCloseSelector:(SEL)inShouldCloseSelector contextInfo:(void 
*)inContextInfo {

     // This method may or may not have to actually present the alert 
sheet.
     if (![self isDocumentEdited]) {

         // There's nothing to do.  Tell the delegate to continue with 
the close.
		// This really is how NSDocument invokes delegate selectors.
         if (inShouldCloseSelector) {
             void (*callback)(id, SEL, NSDocument *, BOOL, void *) = 
(void (*)(id, SEL, NSDocument *, BOOL, void *))objc_msgSend;
             (*callback)(inDelegate, inShouldCloseSelector, self, YES, 
inContextInfo);
         }

     } else {

         // Figure out the window on which to present the alert sheet.  
This should be easy if you only have one window per document.
         NSWindow *documentWindow = [blah blah blah];

         // Create a record of the context in which the panel is being 
shown, so we can finish up when it's dismissed.
         CanCloseAlertContext *closeAlertContext = 
malloc(sizeof(CanCloseAlertContext));
         closeContext->delegate = inDelegate;
         closeContext->shouldCloseSelector = inShouldCloseSelector;
         closeContext->contextInfo = inContextInfo;

         // Present a "save changes?" alert as a document-modal sheet.
         [documentWindow makeKeyAndOrderFront:nil];
         NSBeginAlertSheet(blah, blah, blah, blah, documentWindow, self, 
@selector(canCloseAlertSheet:didEndAndReturn:withContextInfo:), NULL, 
closeContext, blah);

     }

}

