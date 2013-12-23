---
layout: page
title: FinderFlags
---

How can I set the finder flag for a file to invisible (in case some one has the unfortunate need to boot into os 9) in a cocoa application? Thanks!
----
Same way you do it in a carbon application.
----
Yea, I don't know how to do it there either :)
----

Well I havent given up on this and KengoTsuruzono was kind enough to give me the following code which I have found to only work on files. I tried yanking some code from an apple sample code application too but I got the same file not found error. I am totally lost, and wishing I knew carbon. I spent several hours reading through and trying various Carbon API's to no avail. Any way here is that code I was talking about:

    
#import <Cocoa/Cocoa.h>
#import <Carbon/Carbon.h>

OSErr FilePathToFSSpec( NSString* asFilePath, FSSpec* apSpec )
{

   CFURLRef cfUrl = CFURLCreateWithFileSystemPath( kCFAllocatorDefault,
               (CFStringRef) asFilePath, kCFURLPOSIXPathStyle, false );
   FSRef  fileRef;
   OSErr  err = noErr;

   if ( CFURLGetFSRef( cfUrl, &fileRef ) ) {
     err = FSGetCatalogInfo( &fileRef, kFSCatInfoNone, NULL,
           NULL, apSpec, NULL );

     if ( err ) return( err );
   }
   
   CFRelease( cfUrl );
   return( err );

}


OSErr SetFileVisibility( NSString* asFilePath, BOOL abIsVisible ) {
   
    FSSpec  spec;
    FInfo   fInfo;
    OSErr   err;
   
    ///// Get FSSpec

    err = FilePathToFSSpec( asFilePath, &spec );
   
    ///// Get Finder Info

    err = FSpGetFInfo( &spec, &fInfo );
   
    ///// Change Finder Info

    fInfo.fdFlags |= kIsInvisible;
    if ( abIsVisible ) fInfo.fdFlags -= kIsInvisible;

    ///// Set Finder Info

    err = FSpSetFInfo( &spec, &fInfo );

    return( err );
}


int main(int argc, const char *argv[])
{

    NSAutoreleasePool *arp = [ [ NSAutoreleasePool alloc ] init ];
   
    SetFileVisibility( @"/Users/xxx/filePath", YES );
   
    [ arp release ];

    return 0;
}

----
I think the reason you can only do it to a file is in how the FSSpec is obtained, I know the awseome IconFamily class can get the proper references to a folder, perhaps checking their will be useful.

----
Here's an additional routine which gets the file visibility. -- Seth

    
OSErr GetFileVisibility(NSString* asFilePath, BOOL * isVisible)
{
	FSSpec  spec;
	FInfo   fInfo;
	OSErr   err;
	
	///// Get FSSpec
	err = FilePathToFSSpec( asFilePath, &spec );
	
	///// Get Finder Info
	err = FSpGetFInfo(&spec, &fInfo);
	
	///// Change Finder Info
	if (fInfo.fdFlags & kIsInvisible)
		*isVisible = NO;
	else
		*isVisible = YES;
	
	return(err);
}


Additionally, you may wish to test against the filename having '.' as the first character, though this isn't Finder-specific.
isVisible = ([asFilePath characterAtIndex:0] != '.');
----
To be complete, you should also check if the file is listed in     /.hidden.

----

Hey

Just had to do something similar myself. Here is some 10.X compatible code that will make files hidden or visible and will tell you if a file is visible or hidden. (note: I say 10.x as thats what all documentation says (10.0 and up) but I have only tested on 10.4)

    
typedef enum { HIDDEN, VISIBLE, UNKNOWN } Visibility;

-(bool)setFile:(NSString*)filepath visibility:(Visibility)visibility; {
	if(visibility == UNKNOWN) return NO;
	FSRef pathRef;
	FSCatalogInfo catalogInfo;
	if(FSPathMakeRef((const UInt8*)[filepath UTF8String], &pathRef, NULL) != 0) {return NO;}
	if(FSGetCatalogInfo(&pathRef, kFSCatInfoFinderInfo, &catalogInfo, NULL, NULL, NULL) != 0) {return NO;} 
	
	// Set the flag based on user input
	FileInfo *fileInfo = (FileInfo*)&catalogInfo.finderInfo;
	if(visibility == VISIBLE && (kIsInvisible & fileInfo->finderFlags) == kIsInvisible) { fileInfo->finderFlags = (kIsInvisible ^ fileInfo->finderFlags); }
	else if(visibility == HIDDEN) { fileInfo->finderFlags = (kIsInvisible | fileInfo->finderFlags); }

	if(FSSetCatalogInfo (&pathRef, kFSCatInfoFinderInfo, &catalogInfo) != 0) { return NO; }
	return YES;
}
-(Visibility)fileVisibility:(NSString*)filepath; {
	FSRef pathRef;
	FSCatalogInfo catalogInfo;
	if(FSPathMakeRef((const UInt8*)[filepath UTF8String], &pathRef, NULL) != 0) {return UNKNOWN;}
	if(FSGetCatalogInfo(&pathRef, kFSCatInfoFinderInfo, &catalogInfo, NULL, NULL, NULL) != 0) {return UNKNOWN;} 	
	FileInfo *fileInfo = (FileInfo*)&catalogInfo.finderInfo;
	if((kIsInvisible & fileInfo->finderFlags) == kIsInvisible) {return HIDDEN;}
	return VISIBLE;
}




