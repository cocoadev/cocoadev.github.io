---
layout: page
title: NSDirectoryEnumerator
---

NSDirectoryEnumerator searches through directories.

Documented at file:///Developer/ADC%20Reference%20Library/Documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSDirectoryEnumerator.html.

----

NSDirectoryEnumerator is substantially slower than Carbon's FSCatalogSearch. Witness these figures, provided by Alastair Houghton:
    
Carbon (using FSCatalogSearch)              54.9 seconds
Cocoa (using NSDirectoryEnumerator)         148.1 seconds
BSD (using opendir(), readdir() et al.)     129.1 seconds


----

But hardly as convenient. Unless someone has written a category to make it so? Look at the docs: [http://developer.apple.com/documentation/Carbon/Reference/File_Manager/file_manager/function_group_44.html].

----

According to [http://www.mcdevzone.com/weblog/archives/001050.html] FSCatalogSearch will fail if the directory is modified during the search. Anyone know how NSDirectoryEnumerator handles this case?
----
**NOTE**

It also doesn't do much good when it seems that FSCatalogSearch will not start from anywhere other than the root directory. If this is truly the case; NSDirectoryEnumerator, BSD, and the other methods in Cocoa for enumerating directories; will blow the doors off of FSCatalogSearch in most cases.
----
*It iterates a catalog iterator created with     FSOpenIterator where the documentation for the container argument says:*
    
A pointer to an FSRef for the directory to iterate. The set 
of items to iterate over can either be the objects directly 
contained in the directory, or all items directly or 
indirectly contained in the directory (in which case, the 
specified directory is the root of the subtree to iterate). 
See FSRef for a description of the FSRef data type.


*It just says 'the root of the subtree to iterate' - i.e. if you're doing a deep search. I don't see where it says anything about the root directory of the filesystem.*
----
It does. See the description for the first parameter of FSCatalogSearch


*The iterator to use. Objects traversed by this iterator are matched against the criteria specified by the searchCriteria parameter. You can obtain a catalog iterator with the function FSOpenIterator, or with one of the related parameter block calls, PBOpenIteratorSync and PBOpenIteratorAsync. **Currently, this iterator must be created with the kFSIterateSubtree option and the container must be the root directory of a volume.** See FSIterator for more information on the FSIterator data type.*


I did try to pass it a iterator for an arbitrary directory, it does not work.
----

For a quick diversion, does anybody know how to translate from Carbon-style folder/file notation, such as "Macintosh HD:Users:Whomever:Desktop:File" to/from the UNIX style "/Users/Whomever/Desktop/File" -- I recently had to nastily fudge-up some quicktime encoding code which expected the traditional notation. When I passed filenames generated via [NSTemporaryDirectory() stringByAppendingpathComponent: @"SomeTempFile.mov"] to FSMakeSpec() it reported an invalid filename.

Admittedly, I got around it in a general purpose way, but it wasn't "elegant".

---- 
Try NSMutableString, replaceOccurrencesOfString WithString:  

----

This will only work on the startup volume, won't it? It will convert Other HD:MyFolder:MyFile to Other HD/MyFolder/MyFile not /Volumes/Other HD/MyFolder/MyFile. The Carbon File Manager contains     FSRefMakePath and     FSPathMakeRef functions which look like they do this properly. See [http://www.omnigroup.com/mailman/archive/macosx-dev/2001-February/009922.html] for some code to get a FSSpec from a NSString path.
----
It wont even work for the startup volume. *Other HD* does not exist as part of a path, I believe it's the HFS+ volume name. Either way, BSD definitely wont like it.     FSRefMakePath and     FSPathMakeRef are the way to go.

*will     -fileSystemRepresentation perform this conversion?* [http://developer.apple.com/documentation/Cocoa/Reference/Foundation/ObjC_classic/Classes/NSString.html#//apple_ref/doc/uid/20000154/fileSystemRepresentation]

No it won't - what you need to do is take the C string given by -fileSystemRepresentation and pass it to FSPathMakeRef.

----

Performance Note

see [http://www.wodeveloper.com/omniLists/macosx-dev/2002/June/msg00353.html] for an important note about autorelease and NSDirectoryEnumerator.
The main issue is that if you take the normal
    
NSDirectoryEnumerator *enumerator  = [[NSFileManager defaultManager] enumeratorAtPath:  path];
while (curObject = [enumerator nextObject]) {
    NSAutoreleasePool *innerPool = [[NSAutoreleasePool alloc] init];

    // do stuff
    [innerPool release]
}

There are a few possibilities, either the entire loop is within some auto-release pool, or there is an auto-release pool inside the loop (or both).

However using this style will probably cause high memory usage because the [enumerator nextObject] returns strings that are auto-released. There may be a large number of these if a hard disk is being traversed and they won't be auto-released until the end of the loop block, after *every* iteration has been performed.

What is needed instead is something like

    
NSDirectoryEnumerator *enumerator  = [[NSFileManager defaultManager] enumeratorAtPath:  path];
BOOL keepRunning = YES;
while (keepRunning) {
    NSAutoreleasePool *innerPool = [[NSAutoreleasePool alloc] init];
    curObject = [enumerator nextObject];
    if (nil == curObject) {
         keepRunning = NO;
         break;
    }
    // do stuff
    [innerPool release]
}


This way the path string is auto-released every time at the end of the loop.

Arguably this is where a java style iterator (which uses a different pattern) would perhaps be better.

----

You can take advantage of the fact that autorelease pools are nested to make it a little simpler:

    
NSDirectoryEnumerator *enumerator  = [[NSFileManager defaultManager] enumeratorAtPath:  path];
NSAutoreleasePool *innerPool = [[NSAutoreleasePool alloc] init];
while ((curObject = [enumerator nextObject])) {
    // do stuff
    [innerPool release];
    innerPool = [[NSAutoreleasePool alloc] init];
}
[innerPool release];
innerPool = nil;

