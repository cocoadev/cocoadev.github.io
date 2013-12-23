---
layout: page
title: ObtainFreeDiskSpace
---


I am looking for some function calls(cocoa/carbon or bsd) to obtain the free disk space on a machine.

~J

----

How about this:

    
OSErr FSGetVInfo(
  FSVolumeRefNum volume,
  HFSUniStr255 *volumeName,  /* can be NULL */
  UInt64 *freeBytes,      /* can be NULL */
  UInt64 *totalBytes)      /* can be NULL */
{
  OSErr        result;
  FSVolumeInfo    info;
  
  /* ask for the volume's sizes only if needed */
  result = FSGetVolumeInfo(volume, 0, NULL,
    (((NULL != freeBytes) || (NULL != totalBytes)) ? kFSVolInfoSizes : kFSVolInfoNone),
    &info, volumeName, NULL);
  require_noerr(result, FSGetVolumeInfo);
  
  if ( NULL != freeBytes )
  {
    *freeBytes = info.freeBytes;
  }
  if ( NULL != totalBytes )
  {
    *totalBytes = info.totalBytes;
  }
  
FSGetVolumeInfo:

  return ( result );
}


OK, so I cribbed it from MoreFilesX found at http://developer.apple.com/samplecode/MoreFilesX/listing2.html

Use the search function: DiskSpace

----

I use this modified from the DiskSpace

    
+ (unsigned)freeDiskSpace:(NSString*)path {
    unsigned long long sizeValue;
	NSNumber *keyValue;
    NSString *fullPath = [path stringByStandardizingPath];
    if (fullPath) {
		NSFileManager *fm = [NSFileManager defaultManager];
        if (fm) {
            NSDictionary *fileSystemAttributes = [fm fileSystemAttributesAtPath:fullPath];
            if (fileSystemAttributes && [fileSystemAttributes count]) {
				keyValue = [fileSystemAttributes objectForKey:NSFileSystemFreeSize];
                if (keyValue) {
                    sizeValue = [keyValue unsignedLongLongValue];
                    NSLog(@"The current free space on the volume containing \"%@\" is %qu", fullPath, sizeValue);
					return sizeValue; 
                }
            }
        }
    }
	return 0;
}


~J

