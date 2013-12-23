---
layout: page
title: SafelyNamingFiles
---

Here is a convenience function to make sure you do not overwrite any files: http://goo.gl/OeSCu

    
-(NSString *)createSafeFilePathFromBase:(NSString *)creationPath
{
int numberWithName = 1;
BOOL isDir;
NSString *safePath;
safePath = [creationPath retain];

while ([[NSFileManager defaultManager] fileExistsAtPath:safePath
                                            isDirectory:&isDir])
    {
        [safePath release];
        safePath = [[NSString alloc] initWithFormat:@"%@ %d.%@",
            [creationPath stringByDeletingPathExtension],
            numberWithName,[creationPath pathExtension]];
        
        numberWithName++;
    }


return [safePath autorelease];
}


Header:
    
#import <Foundation/Foundation.h>

@interface NSString (EXStringExtras)
-(NSString *)safeFilePath;
@end

Source:
    
#import "EXStringExtras.h"

@implementation NSString (EXStringExtras)
-(NSString *)safeFilePath
{
int numberWithName = 1;
BOOL isDir;
NSString *safePath = [[NSString alloc] initWithString:self];

while ([[NSFileManager defaultManager] fileExistsAtPath:safePath
                                            isDirectory:&isDir])
    {
        [safePath release];
        safePath = [[NSString alloc] initWithFormat:@"%@ %d.%@",
            [self 	stringByDeletingPathExtension],
            numberWithName,[self pathExtension]];
        
        numberWithName++;
    }

return [safePath autorelease];
}

@end


----

This is not quite solid. What if "self" is not a *valid path* to begin with? What if there are *many* numbered files in that folder?

----
I am not sure in what context this is "Safe".  If you have this code running in two programs, they will corrupt each others files.  This is a classic race condition concurrency problem:

Consier this sequence:
Application A checks fileExistsAtPath: and there is no file at the path
Application B checks fileExistsAtPath: and there is no file at the path
Application A writes the file
Application B writes the file thus overwriting the file Application A wrote

Cocoa methods like -writeToFile:atomically: partially address the "safety" issue.

The real question, is why reinvent the wheel particulary when the new invenstion is not as good ?  Why not just use mkstemp() from the Standard C Library which exists exactly for the purpose the code above claims ?

The mkstemp() function makes the same replacement to the template and
     creates the template file, mode 0600, returning a file descriptor opened
     for reading and writing.  This avoids the race between testing for a
     file's existence and opening it for use.


http://developer.apple.com/documentation/Darwin/Reference/ManPages/man3/mkstemp.3.html

Note: creation and namig of temporary files can have serious security impacts.

----

Regarding safety, I'm not sure I understand what you're saying. You mention that this is not a good way to get a "safe" file name but you mention that the -writeToFile:atomically: methods help with the problem. Since this method is not actually writing anything, but giving a currently-safe path to which to write, I don't see how it's problematic in this case, since the string will be handed to the -writeToFile:atomically: method which you say handles the potential race condition correctly.

----
First getting a "safe" name and then _later_ opening and writing the file are what is _not_ "safe".  One problem is that a name that _was_ "safe" when the method above was called may _not_ be "safe" when the path including the name is used via -writeToFile:atomically:.  As shown in the sequence above, this practice can easily result in data corruption and loss particularly when two or more instances of the same program are running.

Another "safety" issue is that overwriting temp files is a well known sucurity subversion technique.  If a rogue program can geuss the name of the next temp file, it may be able to deliberately corrupt the file.

"Safety" in this context is seldom a concern with ordinary applications, but then the whole method above is not needed in ordinary applications.  Standard Cocoa files saving techniques will warn a user before overwriting a file in most cases, and writeToFile:atomically: preserves the last version of the file temporarily.  The suggestion to use the method above at all leads to the question of why, and the answer to why tells us that the above code is NOT safe and is completely redundant with standard library code which is safer.

----

I'm afraid I'm missing something fundamental, then. Why does it matter whether the path is passed directly into -writeToFile:atomically: from a save panel (chosen by a user while other apps are running and can potentially invalidate the path between choice and write) and a method that automatically finds its own potential path. In either case, there's a chance for the chosen path to become invalid. I just don't see any difference whatsoever between the two. Can you be more specific?

That's not to say I'm dismissing the potential security problems, etc. but that's a **completely** separate issue from the implied meaning of this page (finding a 'safe' file name to which to automatically write a file in case the supplied name is already taken). You keep mentioning the security implications, but those pitfalls can exist in any case and that does not appear to be the goal of this particular snippet of code.

Regardless, the use of a UUID for a file name when storing temporary files is the best approach, but **this** code snippet has nothing to do specifically with temporary files. It's just a demonstration of finding an unoccupied variant of a given file path.

----

Agreed. And the excessive use of pointless quoted words should be banned.

