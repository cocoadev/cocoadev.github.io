---
layout: page
title: DiskSpace
---

Does anyone know of a method of getting the free disk space on a volume?

Cheers.

----

easiest way would be to use popen() or NSTask to run the **df** command.  If that's not acceptable, then you can snarf the df command from Darwin and see how they do it.

----

df works beautifully, thanks. :)

----

Interestingly... I found out Apple's own installers use df - when I replaced df with a custom version and my machine wouldn't update. ;)

----

Another method that seems to work just fine...

    
- (void)traceFileSystemAttributesForPath:(NSString*)path
{
    unsigned long long sizeValue;
    NSString *fullPath = [path stringByStandardizingPath];
    if (fullPath)
    {
        NSFileManager *fileManager = [NSFileManager defaultManager];
        if (fileManager)
        {
            NSDictionary *fileSystemAttributes = [fileManager fileSystemAttributesAtPath:fullPath];
            if (fileSystemAttributes && [fileSystemAttributes count])
            {
                NSNumber *keyValue = [fileSystemAttributes objectForKey:NSFileSystemSize];
                if (keyValue)
                {
                    sizeValue = [keyValue unsignedLongLongValue];
                    NSLog(@"The total volume size containing the path \"%@\" is %qu", fullPath, sizeValue);
                }
                
                keyValue = [fileSystemAttributes objectForKey:NSFileSystemFreeSize];
                if (keyValue)
                {
                    sizeValue = [keyValue unsignedLongLongValue];
                    NSLog(@"The current free space on the volume containing \"%@\" is %qu", fullPath, sizeValue);
                }
            }
        }
    }
}

