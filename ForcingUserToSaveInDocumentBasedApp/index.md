---
layout: page
title: ForcingUserToSaveInDocumentBasedApp
---

Hello,

I'm implementing a document-based app where I need the user to save the document in disk as soon as a new document is created. I need that because I will use this information to save extra file to the document's bundle (ex: movies).

I'm adding the following code to <code>windowControllerDidLoadNib</code> event: 

    

[NSApp beginSheet:[NSSavePanel savePanel] modalForWindow:theDocumentWindow modalDelegate:nil didEndSelector:nil contextInfo:nil];



But it doesn't work - the save panel is not attached to the window and any attempt to access it results in beeps.

Any idea?

----
The Cocoa way of doing this would be to create a temporary bundle somewhere to store all the extra files, and not to force the user to do anything they dont' choose to do. Once they decide to save, you can copy the files across from the temporary location to the proper bundle.

----
Thanks! It makes sense.

----
Well... I'm trying to do exactly that (writing the files to a temp folder and then moving them to the document bundle). For that, I'm using <code>writeToFile:ofType:</code> - code below:

    

- (BOOL)writeToFile:(NSString *)fileName ofType:(NSString *)aType {
	
	if ([super writeToFile:fileName ofType:aType]) {
		
		int x;
		int count = [entries count];

		NSString *tempFile;
		NSString *newFile;
		
		for (x=0; x < count; x++) {

		tempFile = [[[NSTemporaryDirectory() stringByAppendingString:@"/"] stringByAppendingString:entries objectAtIndex:x] name stringByAppendingString:@".pdf"];
		newFile = [[[[[[[NSDocumentController sharedDocumentController] currentDocument] fileURL] path] stringByAppendingString:@"/"] stringByAppendingString:entries objectAtIndex:x] name stringByAppendingString:@".pdf"];
			
			// Verify if the file exists at the temporary path
			
			if ([[NSFileManager defaultManager] fileExistsAtPath:tempFile]) {
				
				if (![[NSFileManager defaultManager] fileExistsAtPath:newFile]) {

					[[NSFileManager defaultManager] movePath:tempFile toPath:newFile handler:nil];
					
				}
				
			}
		}
		
		return YES;
	}
	
	return NO;
}



The problem is, the first time the user saves the document, no file is copied - because, it looks like, at that point the path defined by <code>[[[[NSDocumentController sharedDocumentController] currentDocument] fileURL] path]</code> still doesn't exist (the document is being created). The second time I save the file, the operation occurs without any problem.

How do I make sure that the document bundle is created before I start copying the files into it?

----
Why are you using     [[[[NSDocumentController sharedDocumentController] currentDocument] fileURL] path] and not the the filename that's passed to your method as a parameter?

----
You're completely right... changing that to the filename works... thanks!

