---
layout: page
title: GetUTIForFileAtPath
---

I'm writing an application that accepts images dragged onto it from the Finder.  I've implemented drag-and-drop and end up with an array of paths to each file.  That's perfect!  However, I need to get the UTI for each file -- if it's not     public.image, I want to ignore it.

Is there an efficient way to do this?  I've found bits and pieces that indicate that I should be able to do this from the NSPasteboard but it tells me that I'm accepting a file, not what that file's type is.

----

I believe you need to look at the Launch Services functions for manipulating UTIs. Feed them the URL to the file and they'll send back a UTI (remember that you can cast NSURL*s to CFURLRefs and NSString*s to CFStringRefs without any problem and that CFStringRefs returned by C APIs come back with a retain count of 1, if I'm not mistaken, so you have to release them when you don't need them any longer, or autorelease them).

----

Actually, the rules for CoreFoundation reference counting are almost identical to the rules for Cocoa refcounting: if you get a return value from a function that contains the words "Copy" or "Create", or if it's documented as being your responsibility, then you have to release the object, otherwise you don't. The major difference in CF is that there is no autorelease equivalent, and so Copy/Create methods are far more common.

----

Here's what I ended up with (slightly modified from Apple's documentation)

    
Boolean IsProperType(NSURL *fileURL)
{
    Boolean             display = true;
    FSRef               ref;
    
    // Declares a Launch Services item information record. You need this data structure later to get extension and type information for a file.
    LSItemInfoRecord outInfo;
    
    CFURLRef cfURLRef = (CFURLRef)fileURL;
    
    // Get the FSRef for the URL
    if (CFURLGetFSRef(cfURLRef, &ref) == TRUE)
    {
        outInfo.extension = NULL;
        
        // Calls the Launch Services function to obtain the extension and type information for the file.
        // The function LSCopyItemInfoForRef fills the outInfo data structure with the requested information.
        if (LSCopyItemInfoForRef(&ref, kLSRequestExtension|kLSRequestTypeCreator, &outInfo) == noErr)
        {
            CFStringRef itemUTI = NULL;
            
            // Checks to see if the file has an extension. 
            // If it does, the code creates a Uniform Type Identifier (UTI) for the extension
            // by calling the function UTTypeCreatePreferredIdentifierForTag.
            // The Uniform Type Identifier is specified as a CFString object. 
            if (outInfo.extension != NULL) 
            {
                itemUTI = UTTypeCreatePreferredIdentifierForTag (kUTTagClassFilenameExtension,outInfo.extension, NULL);
                CFRelease(outInfo.extension);
            }
            else  
            {
                // If the file does not have an extension, the code creates a UTI from the file type.
                // The file type must first be converted to a CFString object by calling the function UTCreateStringForOSType. 
                // Then you can call the function UTTypeCreatePreferredIdentifierForTag to create a UTI.
                CFStringRef typeString = UTCreateStringForOSType(outInfo.filetype);
                itemUTI = UTTypeCreatePreferredIdentifierForTag(kUTTagClassFilenameExtension, typeString, NULL);
                CFRelease( typeString );
            }
            
            // Checks to make sure a UTI was created.
            if (itemUTI != NULL) 
            {
                // Tests for a conformance relationship between UTI for the file and the public.text type. 
                // Returns true if the types are equal or if the UTI conforms, directly or indirectly, to the second type. 
                // If the UTI is any kind of text (RTF, plain text, Unicode, and so forth), the function UTTypeConformsTo returns true.
                display = UTTypeConformsTo(itemUTI, CFSTR("public.text"));
                
                // Releases the UTI, which is specified as a CFString object.
                CFRelease (itemUTI); 
            }
        }
    }
    return display;
}  

----

Even easier:
    
- (BOOL)isImage:(NSString *)path
{
    BOOL display = FALSE;
    MDItemRef item = MDItemCreate(kCFAllocatorDefault, (CFStringRef)path);
    CFTypeRef ref = MDItemCopyAttribute(item, CFSTR("kMDItemContentType"));
    
    if (ref) {
        if (UTTypeConformsTo(ref, CFSTR("public.jpeg"))) display = TRUE;
        CFRelease(ref);
    }

    if (item) CFRelease(item);
    return display;
}

----

This is now trivial under 10.4 by using LSCopyItemAttribute and the kLSItemContentType key.

    

FSRef		fsRefToItem;
FSPathMakeRef( (const UInt8 *)[file fileSystemRepresentation], &fsRefToItem, NULL );

CFStringRef itemUTI = NULL;
LSCopyItemAttribute( &fsRefToItem, kLSRolesAll, kLSItemContentType, &itemUTI );



Thanks Apple!

----
In shell:

    

mdls -raw -name kLSItemContentType path



----
10.6 and later use NSURL....

    

NSString*   itemUTI = nil;
[itemURL getResourceValue:&itemUTI forKey:NSURLTypeIdentifierKey error:nil];


