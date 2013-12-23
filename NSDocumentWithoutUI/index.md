---
layout: page
title: NSDocumentWithoutUI
---


I am trying to save a subclass of NSDocument   **without** using the Save Panel.
    
    NSDocumentController *docController = [NSDocumentController sharedDocumentController];
    NSError *err = [[NSError alloc] init];
    id doc = [docController openUntitledDocumentAndDisplay:FALSE error:&err];

    [doc writeToURL:[NSURL URLWithString:@"theFile"] ofType:[doc fileType] error:&err];

I have created a subclass of NSDocument that implements writeToURL and readFromURL. I have specified the Document Types, extensions, etc. via the target's "Get Info" pane. 

The file is saved just fine, but for some reason the extension does not appear, nor does the icon and the file seems to be completely ignorant of its file type... The result is that I cannot double-click my document and have it open in the same app... Did I miss a step or will this not even work?

----

Well, you should probably use the built-in NSDocument save/load methods, such as     writeToFile:ofType: and     readFromFile:ofType:, just for compatibility's sake. But the real problem is your     U<nowiki/>RLWithString: method -- you do have to put the extension here. To get an extension from a document type, use     [[[NSDocumentController sharedDocumentController] fileExtensionsFromType:[doc fileType]] objectAtIndex:0]. Then append this extension to your filename before creating the URL. --JediKnil

----

I should mention, I am only targetting Tiger.

Adding the extension to the writeToURL worked. Is it possible to   **not** use an extension, yet have the file/document type and icon be recognized? Is this perhaps a .plist change instead?

----

Haha, sorry. I'm still stuck with Panther, and only looked at my local docs. It looks like you want to use MIMETypes or UniformTypeIdentifiers instead of extensions. Try setting some of these in your target settings. Also, you could look at the old HFS Type/CreatorCode<nowiki/>s, although I think UTIs are recommended by Apple now instead. Finally, you could just hide the extensions on your files by default, because files really are supposed to have extensions now. Hint: you can use looooong extensions like "myFile.specialDocFormat" if you want. --JediKnil

----

I will look into UTIs. Can I programmatically set extensions to be hidden (i.e. for users, as opposed to just myself)? Thanks!

----

Looks like NSFileWrapper's     setFileAttributes: is the way to go. Just check out the NSFileExtensionHidden key.

----

Thanks for all the great suggestions. I have changed my `writeToURL` function to:
    
    - (BOOL)writeToURL:(NSURL *)absoluteURL ofType:(NSString *)typeName error:(NSError **)outError
    {
        NSMutableDictionary *attr = [NSMutableDictionary dictionaryWithCapacity:1];
        // set attributes
        [attr setObject:[NSNumber numberWithBool:TRUE] forKey:NSFileExtensionHidden];
        return [[NSFileManager defaultManager] createFileAtPath:[absoluteURL path]
                                                contents:[NSKeyedArchiver archivedDataWithRootObject:[self toBeArchived]]
                                              attributes:attr]; 
    }

and everything is working exactly as expected! Thanks again.

