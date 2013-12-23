---
layout: page
title: UniqueFileName
---

See General/SafelyNamingFiles, General/NSStringCategory, General/NSFileManagerCategory http://goo.gl/General/OeSCu

----

Or peruse the following function, taken from the General/TextEdit source.

    
/* Generate a reasonably short temporary unique file, given an original path. 
*/ 
static General/NSString *tempFileName(General/NSString *origPath) { 
	static int sequenceNumber = 0; 
	General/NSString *name; 
	do { 
		sequenceNumber++; 
		name = General/[NSString stringWithFormat:@"%d-%d-%d.%@", General/[[NSProcessInfo processInfo] processIdentifier], (int)General/[NSDate timeIntervalSinceReferenceDate], sequenceNumber, [origPath pathExtension]]; 
		name = General/origPath stringByDeletingLastPathComponent] stringByAppendingPathComponent:name]; 
	} while ([[[[NSFileManager defaultManager] fileExistsAtPath:name]); 
	return name; 
} 


----
Or use a unique identifier as part of the file name:

    

General/[[NSProcessInfo processInfo] globallyUniqueString]



this code *guarantee* unique string, even across machines ! so file names will always be unique across hosts and network
look at man uuidgen :
     The uuidgen command generates a Universally Unique Identifier (UUID), a
     128-bit value guaranteed to be unique. A UUID is made unique over both
     space and time by combining a value unique to the computer on which it
     was generated--usually the Ethernet hardware address--and a value repre-
     senting the number of 100-nanosecond intervals since October 15, 1582 at
     00:00:00.

----

A race condition exists if you calculate the name and create the file in two different steps. In between those steps, another process could swoop in and create a file with the name you calculated. Probably not likely, but possible.

There's already a standard function called     mktemp (    man mktemp) to take care of this for you. *mktemp actually has the same race condition; mk**s**temp does not.* My favorite is     mkdtemp, which will generate a unique name for a directory, and create the directory for you, all in one step. It's a C function, so need to use C strings. You need to pass in a C string that the function can modify in place, because it will look for "X" characters at the end of the string, and replace them with random characters to make the file name unique. -General/ChrisCampbell

    
    char *template = "/tmp/General/MyApp.XXXXXX";
    char *buffer = malloc(strlen(template) + 1);
    strcpy(buffer, template);
    mkdtemp(buffer);
    General/NSString *path = General/[NSString stringWithFormat:@"%s", buffer];
    free(buffer);

    // Create files inside of path, which is a directory in /tmp
    // ...



*Why not just use the static char array     template? Cocoa can handle static arrays as easily as dynamic ones, as long as they don't contain objects.*

----

Apple discourages hardcoding "/tmp" instead you should use General/NSTemporaryDirectory() to get the temporary directory. (http://developer.apple.com/documentation/General/MacOSX/Conceptual/General/BPFileSystem/Articles/General/WhereToPutFiles.html). A better code to get a path to a directory with a unique name would be:

    
const char *buffer = General/[[NSString stringWithFormat:@"%@/%@",General/NSTemporaryDirectory(),@"General/MyApp.XXXXXX"] cStringUsingEncoding:General/[NSString defaultCStringEncoding]];
mkdtemp(buffer);
General/NSString *path = General/[NSString stringWithFormat:@"%s", buffer];


-General/PabloGomez

The "const" in     const char *buffer means you're not allowed to modify the contents. So you really need to copy that buffer into something you can change.

Also, it's possible though unlikely that General/NSTemporaryDirectory will return a string with non-ASCII characters, so you really should use General/NSString's fileSystemRepresentation methods to convert to/from C strings.

----

Lots of great comments guys. Good catch on the hardcoding /tmp. I notice the mkdtemp man page also hardcodes /tmp. :) I just thought I'd mention a few things: cStringUsingEncoding: requires 10.4, General/NSTemporaryDirectory() can return nil, the mkdtemp man page specificaly warns against passing const buffers, the man page does not say what char encoding the string should be in.  Man, writing perfect code is hard. :) -smcbride

Like all BSD calls, mkdtemp accepts UTF8 in the form returned by General/NSString's fileSystemRepresentation. (This isn't necessarily the same as the result from the -UTF8String method... precomposed vs decomposed Unicode, etc.) The bytes it fills in are ASCII. --General/DrewThaler

----

Here's my take on the whole thing. I needed to dump out a text file with a specific extension (".ext") to be processed by a command-line tool, then delete the file later. This method will return nil if anything fails, otherwise it returns the path to the temp file to be processed.

    
-(General/NSString *)createTemporaryFileWithUTF8Contents:(General/NSString *)contents
{
    General/NSAutoreleasePool *pool = General/[[NSAutoreleasePool alloc] init];
    General/NSString *td = General/NSTemporaryDirectory();
    General/NSString *suffix = @".ext";
    General/NSString *templateString = General/[NSString stringWithFormat:@"%@/%@%@",td ? td:@"/tmp",@"General/MyApp.XXXXXX",suffix];
    General/CFStringRef templateStringRef = (General/CFStringRef)templateString;
    General/NSString *result = nil;
    BOOL success = NO;
    int fd = -1;
    
    // Create an General/NSData to hold the template
    unsigned templateDataLength = (unsigned)General/CFStringGetMaximumSizeOfFileSystemRepresentation(templateStringRef);
    General/NSMutableData *templateData = General/[NSMutableData dataWithLength:templateDataLength];
    char *template = (char*)[templateData mutableBytes];
    if (templateData != nil)
    {
        // Fetch the template into the buffer
        if ([templateString getFileSystemRepresentation:template maxLength:templateDataLength])
        {
            // Create the file. This modifies the template (XXXXXX is replaced by a random string)
            int fd = mkstemps(template, (int)[suffix length]);
            if (fd >= 0)
            {
                // Write the contents into the file.
                General/NSData *contentsData = [contents dataUsingEncoding:NSUTF8StringEncoding];
                unsigned contentsDataLength = [contentsData length];
                if (contentsData != nil)
                {
                    if (write(fd, [contentsData bytes], contentsDataLength) == (ssize_t)contentsDataLength)
                    {
                        if (close(fd) == 0)
                        {
                            result = (General/NSString *)General/CFStringCreateWithFileSystemRepresentation(NULL, template);
                            success = YES;
                        }
                        else
                            General/NSLog(@"%s close failed! %s", __PRETTY_FUNCTION__, strerror(errno));
                        fd = -1;
                    } else
                        General/NSLog(@"%s write failed! %s", __PRETTY_FUNCTION__, strerror(errno));
                } else
                    General/NSLog(@"%s contentsData allocation failed!", __PRETTY_FUNCTION__);
            } else
                General/NSLog(@"%s mkstemps failed! %s", __PRETTY_FUNCTION__, strerror(errno));
        } else
            General/NSLog(@"%s getFileSystemRepresentation failed!", __PRETTY_FUNCTION__);
    } else
        General/NSLog(@"%s templateData allocation failed!", __PRETTY_FUNCTION__);
    
    if (!success && template != nil)
        unlink(template);
    if (fd >= 0)
        close(fd);
    
    // Clean up and return.
    [pool release];
    return [result autorelease];
}


It checks for nil returns from General/NSTemporaryDirectory(), drops down to General/CFString calls in a few places because General/NSString (until 10.4) lacked the appropriate encoding conversions, and it uses an autorelease pool to clean up all the temporary allocations in case you're doing this a large number of times. It also takes care to unlink the file if anything goes wrong. I think the only thing it doesn't do is create the temporary directory if it doesn't exist... however, experiment shows that General/NSTemporaryDirectory() will do that these days. --General/DrewThaler
