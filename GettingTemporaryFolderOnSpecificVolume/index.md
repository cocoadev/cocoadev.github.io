---
layout: page
title: GettingTemporaryFolderOnSpecificVolume
---



I know there's a way to get the temporary system-wide volume (NSTemporaryDirectory) and there's a way to get system folders in particular domains (FSFindFolder and NSSearchPathForDirectoriesInDomains) but is there are way to get the temporary folder on a particular volume programatically, and without hard-coding in paths? The reason I ask is that I'd like to provide an atomic-write operation in cocoa, and need to store the file I am writing in a temporary location on the same volume as the final destination file. If there are any pointers you have I'd be grateful, thanks! -DavidThorpe

----
FSFindFolder can accept an arbitrary disk and has a kTemporaryFolderType constant, so that should set you up. You'll have to find the vRefNum for the path you're working with which probably isn't 100% straightforward, but certainly doable.

----

Thanks - I will try that. I do note that in the documentation that comes with XCode, "asking for these folders on a per-volume basis yields undefined results. For example, if you were to request the Fonts folder (represented by the selector kFontsFolderType)on volume -100, are you requesting the folder /System/Library/Fonts, /Library/Fonts, or ~/Fonts? On Mac OS X you should pass a disk or domain constant in this parameter." If I find out, I will post my results here - David Thorpe

----

There already is an atomic write operation in Cocoa: -[NSData writeToFile:atomically:].

----

That warning in the docs only applies to certain folder constants, not all of them. So it makes no sense to ask for the Fonts folder on some random disk, but it should make sense to ask for a temporary directory there.

The point about Cocoa already having atomic writes is a good one though.

----

Yes - I know about the NSData thing, but it's not always appropriate unfortunately - I have 2GB files streaming in over a network socket and I don't want to put that into an NSData memory structure before writing it out to disk! Anyway, I think I almost have solved this now, so thanks for the pointers. It may be bad form to answer one's own question, but here's the test code I have written:

    
int main (int argc, const char * argv[]) {
  NSAutoreleasePool * pool = [[NSAutoreleasePool alloc] init];
  // this program accepts one argument
  if(argc != 2) {
    NSLog(@"Syntax error");
    return -1;    
  }
  // get file
  NSString* theFile = [NSString stringWithUTF8String:argv[1]];
  NSLog(@"file => %@",theFile);
  // turn into FSRef
  FSRef outRef;
  OSStatus err = FSPathMakeRef((const UInt8 *)[theFile fileSystemRepresentation], &outRef, NULL);
  if(err != noErr) {
    NSLog(@"ERROR");
    return -1;
  }
  // turn into FSSpec
  FSSpec outSpec;
  err = FSGetCatalogInfo(&outRef,kFSCatInfoNone,NULL,NULL,&outSpec,NULL);
  if(err != noErr) {
    NSLog(@"ERROR");
    return -1;
  }
  NSLog(@"vRef => %d",outSpec.vRefNum);
  // determine the trash folder
  FSRef foundRef;
  err = FSFindFolder(outSpec.vRefNum, kTrashFolderType,kDontCreateFolder,&foundRef);
  if(err != noErr) {
    NSLog(@"ERROR");
    return -1;
  }    
  // turn folder reference back to cocoa-land
  CFURLRef foundURL = CFURLCreateFromFSRef(kCFAllocatorDefault,&foundRef);
  if(foundURL==NULL) {
    NSLog(@"ERROR");
    return -1;    
  }
  CFStringRef foundString = CFURLCopyFileSystemPath(foundURL, kCFURLPOSIXPathStyle);
  if(foundString==NULL) {
    // foundURL has not been released here
    NSLog(@"ERROR");
    return -1;    
  }
  NSString* thePath = [NSString stringWithString:(NSString* )foundString];
  CFRelease(foundURL);
  CFRelease(foundString);
  NSLog(@"found folder = %@",thePath);

  [pool release];
  return 0;
}



The final method would be to firstly look for the "Temporary Items" folder on the volume (kTemporaryFolderType). If an error is returned, then look for the trash folder (kTrashFolderType) and if an error is returned from that than simply store the temporary file in the same folder as the final destination file with a slightly different filename. When the temporary file has been written, delete the original file if it exists and then rename the temporary file. I think this will work in most cases - DavidThorpe

----
Looks good, but I have some small comments on the code:


*     stringWithCString: should be avoided at all costs. The appropriate method to use here is     stringWithUTF8String:. See StringWithCString for why this is.
* FSSpec is very old and crusty. If there is any way to accomplish what you need without using one, you should do it. I'm sure you can get the vRefNum of a path in some other way.
* Your dance to get     thePath is unnecessary. You can simply write     NSString *thePath = [(NSString *)foundString autorelease]; and then dispense with the     CFRelease(foundString);. Your way works, it's just a little more complicated.


----
I don't think using the Trash is such a good idea.  I'd hate for my trash to turn full and then empty randomly, and the OS monitors changes to the Trash folder in order to accomplish this, which then makes a round-trip to the WindowServer.

There are obviously Cocoa apps out there that can deal with very large files.  Maybe you could create a set of smaller NSData objects (maybe 128 MB) and let paging take care of swapping until you're ready to collect all the data and write it to disk?

----
Thanks for your tips and advice! - re: NSData buckets - nice idea, but somewhat defeats the purpose of atomically writing a file to disk from a network stream of data. I'm going to go with the Temporary Items Folder / fallback on the Trash folder plan. Thanks again, DavidThorpe.

----
Not sure what you mean by "defeats the purpose"... when your data transmission is done, you can collect all the NSData buckets together in one NSData and write that to disk atomically.

----

The gathering would have to be done in-memory, which I can't do because of some of the data sizes (I'd like to be able to support over 4GB). Therefore would have to write out the NSData 128Mb buckets to disk and then append them to each other. I'd need to find some temporary location to do this as I don't want to write the file atomically. Therefore, we're pretty much back where we started!

----
Good point.  So the actual streaming of the data does not need to be atomic, just the creation of the final resulting file?

----

This is a cleaned up version of the above code that actually gets the temporary directory of the target volume. It's written as a category on NSFileManager. This is my first post here btw. Woot! -AndyKim

    

@implementation NSFileManager (PotionFactory)

- (NSString *)temporaryDirectoryForAtomicWriteToDirectory:(NSString *)path
{
	OSStatus err;
	
	// Check if the directory to write to exists first
	BOOL isDir;
	if (!([self fileExistsAtPath:path isDirectory:&isDir] && isDir)) return nil;

	// Turn into FSRef
	FSRef outRef;
	err = FSPathMakeRef((const UInt8 *)[path fileSystemRepresentation], &outRef, NULL);
	if (err != noErr) return nil;

	// Get volume ref number
	FSCatalogInfo catalogInfo;
	err = FSGetCatalogInfo(&outRef, kFSCatInfoVolume, &catalogInfo, NULL, NULL, NULL);
	if (err != noErr) return nil;
	
	// Determine the temporary folder, creating it if necesssary
	FSRef foundRef;
	err = FSFindFolder(catalogInfo.volume, kTemporaryFolderType, kCreateFolder, &foundRef);
	// NOTE: This is the most likely place to fail if the directory exists.
	//       Some volumes don't have a temporary folder and don't allow its creation.
	if (err != noErr) return nil;

	// Turn folder reference back to cocoa-land
	NSURL *foundURL = [(NSURL *)CFURLCreateFromFSRef(kCFAllocatorDefault, &foundRef) autorelease];
	if (foundURL == nil) return nil;
	
	// I got it biotch
	CFStringRef foundString = CFURLCopyFileSystemPath((CFURLRef)foundURL, kCFURLPOSIXPathStyle);
	if (foundString == NULL) return nil;
	
	return [(NSString *)foundString autorelease];
}

@end



----

Note that in Leopard, FSGetTemporaryForReplaceObject (defined in     <CarbonCore/Files.h>) exists for the express purpose of getting a temporary directory appropriate for using the new     FSReplaceObject/    FSPathReplaceObject API.  --K

