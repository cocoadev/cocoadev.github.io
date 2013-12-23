---
layout: page
title: NSPathUtilities
---

NSString path-oriented methods such as     stringByStandardizingPath and     lastPathComponent are declared at /System/Library/Frameworks/Foundation.framework/Headers/NSPathUtilities.h. 

    
public static NSDictionary fileAttributes( String filePath, boolean resolveSymlink)


Description found!

this is what it returned for us:
NSDictionary:
*NSFileGroupOwnerAccountName = theGroup; 
*NSFileModificationDate = 2001-07-13 15:04:33 -0700; 
*NSFileOwnerAccountName = theUser; 
*NSFilePosixPermissions = 420; 
*NSFileReferenceCount = 1; 
*NSFileSize = 39273; 
*NSFileSystemFileNumber = 197077; 
*NSFileSystemNumber = 234881033; 
*NSFileType = { NSFileTypeRegular, NSFileTypeSymbolicLink, ...} ; 

FrancoisFrisch

----

WARNING when using
    
public static NSDictionary stringByResolvingSymlinksInPath( String filePath)


This method resolves any symlinks in the path HOWEVER it also strips the trailing "/"!

FrancoisFrisch

----

enums in NSPathUtilites.h:

    
FOUNDATION_EXPORT NSString *NSUserName(void);
FOUNDATION_EXPORT NSString *NSFullUserName(void);

FOUNDATION_EXPORT NSString *NSHomeDirectory(void);
FOUNDATION_EXPORT NSString *NSHomeDirectoryForUser(NSString *userName);

FOUNDATION_EXPORT NSString *NSTemporaryDirectory(void);

FOUNDATION_EXPORT NSString *NSOpenStepRootDirectory(void);

typedef enum {
    NSApplicationDirectory = 1,		// supported applications (Applications)
    NSDemoApplicationDirectory,		// unsupported applications, demonstration versions (Demos)
    NSDeveloperApplicationDirectory,	// developer applications (Developer/Applications)
    NSAdminApplicationDirectory,	// system and network administration applications (Administration)
    NSLibraryDirectory, 		// various user-visible documentation, support, and configuration files, resources (Library)
    NSDeveloperDirectory,		// developer resources (Developer)
    NSUserDirectory,			// user home directories (Users)
    NSDocumentationDirectory,		// documentation (Documentation)
#if MAC_OS_X_VERSION_10_2 <= MAC_OS_X_VERSION_MAX_ALLOWED
    NSDocumentDirectory,		// documents (Documents)
#endif
    NSAllApplicationsDirectory = 100,	// all directories where applications can occur
    NSAllLibrariesDirectory = 101	// all directories where resources can occur
} NSSearchPathDirectory;

typedef enum {
    NSUserDomainMask = 1,	// user's home directory --- place to install user's personal items (~)
    NSLocalDomainMask = 2,	// local to the current machine --- place to install items available to everyone on this machine (/Library)
    NSNetworkDomainMask = 4, 	// publically available location in the local area network --- place to install items available on the network (/Network)
    NSSystemDomainMask = 8,	// provided by Apple, unmodifiable (/System)
    NSAllDomainsMask = 0x0ffff	// all domains: all of the above and future items
} NSSearchPathDomainMask;

FOUNDATION_EXPORT NSArray *NSSearchPathForDirectoriesInDomains(NSSearchPathDirectory directory, NSSearchPathDomainMask domainMask, BOOL expandTilde);

