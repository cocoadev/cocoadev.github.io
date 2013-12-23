---
layout: page
title: SaveDocumentToPathWhenNewDocumentCreated
---

----
Sorry, Ignore this page please...  I had some other code that was causing the problem.

----
I have a project where I want to store potentially a large portion of media assets (and others) that will take up a lot of space.  I will be using a folder based document to manage all of these assets.  However, I want to enforce creating/saving a file name when a new document is created, in the same manner than iMovie does.  I assume iMovie does it for the same reasons:  It is easier to simply know where to dump "scratch" type media files if the document already has a path, rather than trying to save them to a temporary folder and then have to copy them upon save.  It also prevents having to copy all of these assets over if the final save destination is on a different partition than the temporary folders are.

I have tried to make my own subclass of NSDocumentController and override the makeUntitledDocumentOfType:error: method, but I get an error every time.

Code:
    
- (id)makeUntitledDocumentOfType:(NSString *)typeName error:(NSError **)outError
{
    // get document
    id doc = [super makeUntitledDocumentOfType:typeName error:outError];
    
    // got a doc?
    if (doc) {
        // got a doc, force save
        NSLog(@"We got a doc: %@", doc);
        [doc saveDocumentAs:nil];
    } else {
        NSLog(@"No doc created...");
    }
    
    // return doc
    return doc;
}


Everything looks like it is working, but when I hit "Save" in the save panel, I get the following error message:

The document �Untitled� could not be saved as �My First File�.

It would seem that I need to maybe call the save message further down the line, as my guess is that the error is caused by the document not being fully initialized or ready yet.  However, I have no idea where I can put the save message to have it work properly.  I also tried to override openUntitledDocumentAndDisplay:error: but it also gave me the same error.

