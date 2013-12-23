---
layout: page
title: NSFileAttributes
---



NSFileManager lets you get file attributes with the method     fileAttributesAtPath:traverseLink:. NSFileManager also defines an NSDictionary category that allows you to easily access individual attributes so you can do this:

        int fileSize = [fileAttributes fileSize]

instead of this:

        int fileSize = [fileAttributes objectForKey:NSFileSize]

Here's the interface for the file attributes category additions to NSDictionary
    
@interface NSDictionary (NSFileAttributes)

- (unsigned long long)fileSize;
- (NSDate *)fileModificationDate;
- (NSString *)fileType;
- (unsigned long)filePosixPermissions;
- (NSString *)fileOwnerAccountName;
- (NSString *)fileGroupOwnerAccountName;
#if !defined(__WIN32__)
- (unsigned long)fileSystemNumber;
- (unsigned long)fileSystemFileNumber;
#endif	/* ! __WIN32__ */
- (BOOL)fileExtensionHidden;
- (OSType)fileHFSCreatorCode;
- (OSType)fileHFSTypeCode;
#if MAC_OS_X_VERSION_10_2 <= MAC_OS_X_VERSION_MAX_ALLOWED
- (BOOL)fileIsImmutable;
- (BOOL)fileIsAppendOnly;
- (NSDate *)fileCreationDate;
- (NSNumber *)fileOwnerAccountID;
- (NSNumber *)fileGroupOwnerAccountID;
#endif
@end


