---
layout: page
title: FSMakeFSSpec
---

** Converting NSString Paths to FSSpecs **
----
Converting NSString Paths to FSSpecs can be tricky, especially when dealing with paths that do not yet exist.

Here's the best way to do this:

    

OSStatus PathToFSSpec (NSString *path, FSSpec *outSpec)
{
    OSStatus err = noErr;
    FSRef ref;
    Boolean isDirectory;
    FSCatalogInfo info;
    CFStringRef pathString = NULL;
    CFURLRef pathURL = NULL;
    CFURLRef parentURL = NULL;
    CFStringRef nameString = NULL;

    const char *inPath = [path cString];

    // First, try to create an FSRef for the full path 
    if (err == noErr) {
        err = FSPathMakeRef ((UInt8 *) inPath, &ref, &isDirectory);
    }
    
    if (err == noErr) {
        // It's a directory or a file that exists; convert directly into an FSSpec:
        err = FSGetCatalogInfo (&ref, kFSCatInfoNone, NULL, NULL, outSpec, NULL);
    } else {
        // The harder case.  The file doesn't exist.
        err = noErr;
    
        // Get a CFString for the path
        if (err == noErr) {
            pathString = CFStringCreateWithCString (CFAllocatorGetDefault (), inPath, CFStringGetSystemEncoding ());
            if (pathString == NULL) { err = memFullErr; }
        }
    
        // Get a CFURL for the path
        if (err == noErr) {
            pathURL = CFURLCreateWithFileSystemPath (CFAllocatorGetDefault (), 
                                                    pathString, kCFURLPOSIXPathStyle, 
                                                    false /* Not a directory */);
            if (pathURL == NULL) { err = memFullErr; }
        }
        
        // Get a CFURL for the parent
        if (err == noErr) {
            parentURL = CFURLCreateCopyDeletingLastPathComponent (CFAllocatorGetDefault (), pathURL);
            if (parentURL == NULL) { err = memFullErr; }
        }
        
        // Build an FSRef for the parent directory, which must be valid to make an FSSpec
        if (err == noErr) {
            Boolean converted = CFURLGetFSRef (parentURL, &ref);
            if (!converted) { err = fnfErr; } 
        }
        
        // Get the node ID of the parent directory
        if (err == noErr) {
            err = FSGetCatalogInfo(&ref, kFSCatInfoNodeFlags|kFSCatInfoNodeID, &info, NULL, outSpec, NULL);
        }
        
        // Get a CFString for the file name
        if (err == noErr) {
            nameString = CFURLCopyLastPathComponent (pathURL);
            if (nameString == NULL) { err = memFullErr; }
        }
        
        // Copy the string into the FSSpec
        if (err == noErr) {	
            Boolean converted = CFStringGetPascalString (nameString, outSpec->name, sizeof (outSpec->name), 
                                                        CFStringGetSystemEncoding ());
            if (!converted) { err = fnfErr; }
			
        }
    
        // Set the node ID in the FSSpec
        if (err == noErr) {
            outSpec->parID = info.nodeID;
        }
    }
        
    // Free allocated memory
    if (pathURL != NULL)    { CFRelease (pathURL);    }
    if (pathString != NULL) { CFRelease (pathString); }
    if (parentURL != NULL)  { CFRelease (parentURL);  }
    if (nameString != NULL) { CFRelease (nameString); }
    
    return err;
}



Note that this code is an improved version of the code at:

http://www.opensource.apple.com/darwinsource/10.3/Kerberos-47/KerberosFramework/Common/Sources/FSpUtils.c


----

Eric Brunstad

Mind Sprockets Software

http://www.mindsprockets.com

----

** Other Methods **

----

I'm writing a QuickTime tool in Cocoa, and I'm going crazy trying to figure out why I'm getting an OSErr=-37 when I try to FSMakeFSSpec an FSSpec for a simple full path. The error code descriptions are at the link below:

http://developer.apple.com/documentation/Carbon/Reference/File_Manager/file_manager/ResultCodes.html

Basically OSErr=-37 is a bad filename or volume name (bdNamErr). When a full pathname is only four levels deep, everything works fine, but when a full pathname is more than four levels deep I get this error. 

    

*extern OSErr FSMakeFSSpec( short vRefNum, long dirID, ConstStr255Param fileName, FSSpec *spec)
        
struct FSSpec {
    short              vRefNum;           //  Volume reference number.
    long               dirID;                  //  Directory ID of parent directory.
    StrFileName    name;                 //  Filename or directory name; a Str63 string on the Mac OS
};
    


The full pathnames do not have spaces in them or any other funky characters for that matter. All of the path components are less than 10 characters each, so I don't think this is being caused by any limits to the length of the pascal string passed to this function. I'm not an experienced classic Mac programmer, so any help from one would be nice --zootbobbalu

----

OK, here's what I figured out. FSMakeFSSpec isn't the function you should be using for full pathnames. You should be using FSPathMakeRef and then FSGetCatalogInfo to create an FSSpec for a full pathname. Here's an example (slightly modified) that I found in a mail archive. 

http://www.mail-archive.com/cocoa-dev@lists.apple.com/msg16096.html

Just add this class method to any custom class to get an FSRef for a full pathname or a symlink. 

    
+ (OSErr)getFSRefAtPath:(NSString*)sourceItem ref:(FSRef*)sourceRef
{
    OSErr    err;
    BOOL    isSymLink;
    id manager=[NSFileManager defaultManager];
    NSDictionary *sourceAttribute = [manager fileAttributesAtPath:sourceItem
traverseLink:NO];
    isSymLink = ([sourceAttribute objectForKey:@"NSFileType"] ==
NSFileTypeSymbolicLink);
    if(isSymLink){
        const char    *sourceParentPath;
        FSRef        sourceParentRef;
        HFSUniStr255    sourceFileName;
        
        sourceParentPath = (UInt8*)sourceItem
stringByDeletingLastPathComponent] fileSystemRepresentation];
        err = [[FSPathMakeRef(sourceParentPath, &sourceParentRef, NULL);
        if(err == noErr){
            sourceItem lastPathComponent]
getCharacters:sourceFileName.unicode];
            sourceFileName.length = [[sourceItem lastPathComponent] length];
            if (sourceFileName.length == 0){
                err = fnfErr;
            }
            else err = [[FSMakeFSRefUnicode(&sourceParentRef,
sourceFileName.length, sourceFileName.unicode, kTextEncodingFullName,
sourceRef);
        }
    }
    else{
        err = FSPathMakeRef([sourceItem fileSystemRepresentation],
sourceRef, NULL);
    }
    
    return err;
}


once you have an FSRef you can get an FSSpec by making the following call (FSGetCatalogInfo). Here's a complete example:

    
FSRef fsRef;
FSSpec fsSpec;
OSErr getFSRefAtPathError=[CustomClass getFSRefAtPath:path ref:&fsRef];
OSErr getCatalogInfoError=FSGetCatalogInfo(&fsRef, 
                                kFSCatInfoNone, 
                                NULL,
                                NULL,
                                &fsSpec,
                                NULL);


--zootbobbalu

----

Or you could just use the CFURLGetFSRef() function on an NSURL. -- FinlayDobbie

----

Thanks for everyone's help... I was getting the -37 error from FSMakeFSSpec and it turns out that the name I was giving it wasn't proper Str255 format. In any case, I *really* needed to get an FSSpec for a file that doesn't exist to pass to another function (which WILL create the file in at the passed FSSpec location) . After HOURS of work I finally figured it out and thought I'd share:

    
		// SAVE
		// create FSpec for the NEW file, without actually CREATING the new file...
		OSErr		err;
		FSSpec		fspecNewFile, fspecParentDir;
		FSRef		frefParentDir;
		OSStatus	status;
		
		// get FSRef to parent - dirPath is a NSString containing "/path/to/enclosing/dir/for/newfile
		status = FSPathMakeRef ([dirPath fileSystemRepresentation],
								&frefParentDir, 
								NULL);
		NSAssert(status == 0, @"Creating ref to enclosing dir failed.");
		
		err = FSGetCatalogInfo (
								&frefParentDir,
								kFSCatInfoNone,
								NULL,
								NULL,
								&fspecParentDir,
								NULL
								);	
		NSAssert1(err == 0, @"Converting to FSSpec failed: (%i)", err);
		
		// get dirID of the parent Directory (since the fspec only has the dir's PARENT dirID)
		CInfoPBRec pb;
		bzero(&pb, sizeof(pb));
		pb.dirInfo.ioFDirIndex = 0; // get info about the DIR with the NAME and PARENT specified
		pb.dirInfo.ioVRefNum = fspecParentDir.vRefNum; // this was missing before (Bob Ippolito)
		pb.dirInfo.ioNamePtr = fspecParentDir.name;
		pb.dirInfo.ioDrDirID = fspecParentDir.parID;
		
		err = PBGetCatInfoSync(&pb);
		NSAssert(err == 0, @"Error getting catalog info.");

		// need a name for this string. it's not important, as DecodeMacBinary will REPLACE this name with the proper one.
		Str255  tmpStr;
		bzero(&tmpStr, sizeof(tmpStr));
		tmpStr[0] = 1;
		tmpStr[1] = 's';
		bzero(&fspecNewFile, sizeof(fspecNewFile));
		
		err = FSMakeFSSpec (
							fspecParentDir.vRefNum,
							pb.dirInfo.ioDrDirID,
							tmpStr,
							&fspecNewFile
							);
		NSAssert1(err == 0 || err == fnfErr, @"Creating FSSpec failed: (%i)", err);


-- Alan Pinstein

----
I found a useful function to answer the FSSpec demands of CocoaDev users! You need carbon framework linked in.....

*note: this function is now marked DEPRECATED --zootbobbalu*
    
	FSSpec fsSpec;

	HRUtilGetFSSpecFromURL([@"file:///path/to/parent/" cString], [@"filename.aiff" cString], &fsSpec);


And bam! You got your fsspec in basically one line of code. The string i used is hardcoded, but it'd be easy enough to modify that line to be a straight up NSString path or NSURL to fsspec.

GormanChristian

*Remember that     -cString and any other CString <-> NSString functions are unsafe (see discussion on StringWithCString). You should probably use     -UTF8String instead, which will work the same for any ASCII characters in the path. --JediKnil*

Actually, in this case, both parameters are URLs and so they must be properly URL-encoded. Given that, they won't contain any non-ASCII characters and so cString is safe to use. It's still something you should avoid just for habit's sake, but in this specific case it will work fine.


----
Tiger (MacOSX 10.4) has FSPathMakeRefWithOptions that could resolve symlinks.
----
This is a great page.  My thanks go to all contributors.

However, this page is a great example of why many Mac applications do not explicitly use FSSpec.  Let's be realistic, FSSpec is an abomination that should have died with Classic.  Like it or not, paths and more generally URLs and most specifically POSIX path API won the API wars.  FSSpec is non-portable and actually less functional regarding symbolic links and auto-mounted file systems etc.

If Apple expects any developer to write 60+ lines of code in order to open a file, then Apple expects developers to abandon Apple.  It is that simple.

The good news is that developers are free to ignore "stuff" like FSSpec and just use the cross platform well supported POSIX APIs or Cocoa.
----
Part of the reason FSSpec is supported in Carbon is for compatiblity with Mac OS 8.x, which did not support FSRef.
----
Posix APIs and Cocoa do not handle Aliases, and there are other cases, too (HFS+ file system info such as hidden bits), see documentation.
----
POSIX and Cocoa also don't support paths longer than 1024character. FSRef s are the best way to go.
----
FSRef doesn't handle aliases either, you have to resolve them manually if you got a path which may contain them.
----
Yes. FSRef is not the same thing as FSSpec, and specificly it is FSSpec that's cumbersome to use.
There is no reason to have the one millionth debate about aliases (good/bad) vs. symbolic links or hard links vs. network file systems and network home directories etc.

