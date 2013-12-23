---
layout: page
title: CocoaEncyrption
---

NSWorkspaceCompressOperation	Compress file. Currently unavailable.
NSWorkspaceDecompressOperation	Decompress file. Currently unavailable.
NSWorkspaceEncryptOperation	Encrypt file. Currently unavailable.
NSWorkspaceDecryptOperation	Decrypt file. Currently unavailable.


Mostly I want the encryption features...anyone know how I can implement them in a equally simple manner? Im guessing they are unavailable  because os x finder can not do it yet....like it could in nine. Which then made me think hmm maybe I can just use a carbon call, but I could not find anything. I think I just wasn't looking in the right place.

--GormanChristian

I think they are available. SNAX uses them, I believe.

-- DavidRemahl

Well I just tried them....maybe I did it wrong.

    
NSFileManager *fileManager;
NSArray *simple;

fileManager = [[NSFileManager alloc] init];
fileManager = [NSFileManager defaultManager];

simple = [[NSArray alloc] initWithObjects:(NSString *)
                          @"ducktape2 copy.jpg",nil ];
NSLog(@"%d", [fileManager fileExistsAtPath:
                       @"/hello/ducktape2 copy.jpg"]);
 
NSLog(@"%d",[[NSWorkspace sharedWorkspace]
                  performFileOperation:NSWorkspaceEncryptOperation
                                source:@"/hello"
                           destination:@""
                                 files: simple
                                   tag: nil]);


Output:

1 (File does exist)

0 (No, I won't encrypt your file foo!)

When I throw in another operation, like say NSWorkspaceDestroyOperation, it works fine. And of course I didn't backup that picture. Oh well.

--GormanChristian

----

No, I'm pretty sure these operations (Compress/Decompress/Encrypt/Decrypt) really don't do anything. That's what "unavailable" means. So 0 means "I don't understand your request ..."

-- MikeTrent

