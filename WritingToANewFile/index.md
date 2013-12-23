---
layout: page
title: WritingToANewFile
---

I've been looking all over, but can't seem to find the answer to this simple question: How do I write to a file that doesn't exist yet in Objective C?  That is, how do I create a new file and then write to it?  The closest answer I can find is this post, here: General/FileIOandParsing.

But I don't really understand what's going on with the call to "open".  Plus, I'm just shocked that there isn't some easy way with the General/NSFileHandle class.

----

What kind of file do you want to write?  General/NSString has a writeToFile:atomically: method that will write the contents of the receiver to the named file.  Many of the core Foundation classes have the same kind of thing, I believe. (Can think of General/NSData, General/NSDictionary right off hand)

----

Any     writeToFile:(General/NSString *)aPath method will create the file if it doesn't exist.

----

Thanks to both of the previous posters. The reason I missed the writeToFile method is that, at least in my tests, it doesn't succeed if the path to the file you are creating doesn't exist.  I'm finding more related info now: In General/NSFileManager, there's createDirectoryAtPath:attributes: and createFileAtPath:contents:attributes:.

----

Are you expanding tildes in the paths you're writing to?     [@"blah" writeToFile:[@"~/test.txt" stringByExpandingTildeInPath] atomically:YES] works just fine.

----

How do you delete a file in Objective C? *Answering myself: General/NSWorkspaceDestroyOperation in General/NSWorkspace looks promising.*

----

See: General/[NSFileManager - (BOOL)removeFileAtPath:(General/NSString *)path handler:(id)handler]
