---
layout: page
title: SaveDocumentWithoutNSSavePanel
---



Is it possible to save a document in a document-based application without an NSSavePanel being displayed? I want to do this as soon as the document has been created. You'll just have to take my word that what I'm doing doesn't break interface guidelines. I am imagining something similar to what Xcode does when you create a project.

Basically the user sees this:
1. They click New Document (from the file menu)
2. A document window is displayed and from it drops a configuration window
3. Once they click Continue on the configuration window, the document is invisibly saved
4. They can continue modifying the document, and when they choose File->Save, it saves the document without posting an NSSavePanel

It must be clean way to do this.

I have tried calling NSDocument's -setFileName: and -setFileType: methods, then calling -saveDocument:, but it posts a window that the save location couldn't be found.
I have also tried overriding NSDocument's -fileTypeFromLastRunSavePanel and -fileNameExtensionWasHiddenInLastRunSavePanel which works, but is so ugly I can't bring myself to use it. The problem is that I can't reliably tell when to return what was actually in the save panel ([super file...]), and when to return my hacked values. In my opinion, this has no place in a commercial application.

Any help with this would be greatly appreciated.
Thanks for your time.
-- EliotSimcoe

----

How about the NSDocument method
 <code>

- (void) saveToFile: (NSString *)fileName saveOperation: (NSSaveOperationType) saveOperation 
delegate: (id)delegate didSaveSelector: (SEL)didSaveSelector contextInfo: (void *)contextInfo

</code>

*Called after the user has been given the opportunity to select a destination through the modal Save panel presented by runModalSavePanelForSaveOperation:delegate:didSaveSelector:contextInfo:. The delegate is assigned to the Save panel. If fileName is non-nil, this method writes the document to fileName, sets the document�s file location and document type (if a native type), and clears the document�s edited status. didSaveSelector gets called with YES if the document is saved successfully, and NO otherwise. The saveOperation is one of the constants in �Constants�. Pass contextInfo with the callback.*

[http://developer.apple.com/documentation/Cocoa/Reference/ApplicationKit/ObjC_classic/Classes/NSDocument.html]

----

When I set the document's filename and type using     -(void)setFileName:(NSString *)fileName

and     -(void)setFileType:(NSString *)fileType respectively, then call

 <code>

- (void) saveToFile: (NSString *)fileName saveOperation: (NSSaveOperationType) saveOperation 
delegate: (id)delegate didSaveSelector: (SEL)didSaveSelector contextInfo: (void *)contextInfo

</code>

with save operation == NSSaveOperation, I get the following error:

**
2003-11-20 07:07:12.075 ImpulseAppKitTest[3031] *** Assertion failure in -[IAKTDocument writeToFile:ofType:], AppKit.subproj/NSDocument.m:1105
2003-11-20 07:07:12.078 ImpulseAppKitTest[3031] Invalid parameter not satisfying: type
**

What is happening, is 
 <code>

- (void) saveToFile: (NSString *)fileName saveOperation: (NSSaveOperationType) saveOperation 
delegate: (id)delegate didSaveSelector: (SEL)didSaveSelector contextInfo: (void *)contextInfo

</code>

 assumes that it has been called after some internal haquery has been done by Apple, and    - (NSString *)fileTypeFromLastRunSavePanel has been set to return the type. It DOES NOT determine the type from the extension of fileName and it DOES NOT use the    -(NSString *)fileType method to access what the document was last saved as. Therefore, I am going to need to either re-write a significant part of the document saving structure, or take another route entirely...

Any Suggestions?
Thanks again!
-- EliotSimcoe

----

I'm facing exactly the same problem, but I stumbled across something that seems to work. Immediately after calling     writeToFile:ofType:, I revert the document by calling     revertToSavedFromFile:ofType:. This does not have any visible effect on the document, but it apparently does whatever needs to be done to avoid the "location could not be determined" error.

----

This is not clear enough. I've got the same problem but I haven't tried to set the file type, I just tried     -(void)setFileName:(NSString *)fileName and     -(void)setFileURL:(NSString *)fileName and I got the same message when I tried to save the document (although the doc window does show the name given to the file). the way I see it,     setFileName would be the method called when (in a "normal" document based application) the user selects some destination using the save (or save as) GUI. After that, the App knows where the file is (and must be saved) but it seems that's not the logic behind     setFileName. 

Does anyone have any other suggestions?

... later

I've now tried the previous poster's workaround, but it didn't work for me, the file is saved, it seems to be reverted from the saved URL (that's what the method returns, a YES), but when trying to save it again I get the same message. 

----

This is what worked for me. In 10.4 the initial save of a document invokes     saveDocumentWithDelegate:didSaveSelector:contextInfo: which by default calls     runModalSavePanelForSaveOperation:delegate:didSaveSelector:contextInfo: and then calls     saveToURL:ofType:forSaveOperation:delegate:didSaveSelector:contextInfo:. The solution is to override and skip the call to     runModalSavePanelForSaveOperation: and provide your own absolute URL to the call to     saveToURL:

     

- (void)saveDocumentWithDelegate:(id)delegate didSaveSelector:(SEL)didSaveSelector contextInfo:(void *)contextInfo
{

              NSURL *theFileURL; // do whatever you need to do to make this exist
	
		[self saveToURL:theFileURL ofType:@"your type" forSaveOperation:NSSaveOperation delegate:delegate didSaveSelector:didSaveSelector contextInfo:contextInfo];
		[self updateChangeCount:NSChangeCleared];

}



BarryWilson

----

The following code works for me with a Core Data Document Based app.  It looks like setFileURL: determines if the file is there or not when it's run, and that's what prompts the 'save location can't be found' warning.  As long as I write to the file prior to setting it's URL it works for me.

     

	NSURL *fileURL = [NSURL fileURLWithPath:filePath]; // Get your file URL however
	NSError *writeError = nil;
	[self writeToURL:fileURL ofType:@"your type" forSaveOperation:NSSaveToOperation originalContentsURL:nil error:&writeError];
	[self setFileURL:fileURL];



BobWarwick

