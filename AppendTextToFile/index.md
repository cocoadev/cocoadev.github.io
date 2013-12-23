---
layout: page
title: AppendTextToFile
---

How do I append text to a file instead of rewrite?

----

What would be wrong with reading the file, appending to the string, then resaving the file?  Unless you're dealing with a huge amount of text, the performance will be fine:

    
NSString * myFilePath = @"/path/to/file.txt";
NSString * myText = [NSString stringWithContentsOfFile:myFilePath];

myText = [myText stringByAppendingString:@"blablabla"];

[myText writeToFile:myFilePath atomically:YES];


----
The problem is that the text will get really huge (it's a server log)? Ah.. My bad.

is there a better way?

or should I just make a new log file for every week?

If you are running 10.3, you could look at NSOutputStream.  It has initToFileAtPath:append: and write:maxLength: methods.  I've never used it, but it sounds like it might do what you want.

----

Open the file as normal with NSFileHandle *handle = [NSFileHandle fileHandleForWritingAtPath:@"path"]
Then seek to the end of the file: [handle seekToEndOfFile];

Or you use fopen("path", "a") to get a file descriptor and turn that into an NSFileHandle with [[NSFileHandle alloc] initWithFileDescriptor:fd];

*This is exactly what [NSFileHandle fileHandleForUpdatingAtPath:...] is for.*

----

I believe fileHandleForUpdatingAtPath: is if you want to read AND write, rather than for appending vs. overwriting. The open and jump to end mentioned above (fileHandleForWritingAtPath: followed by seekToEndOfFile) seems to be the simplest solution.

----

Unfortunately, that approach has a race condition if there's any possibility of other processes modifying the file at the same time. Opening the file in append mode means your data will *always* go at the end even if somebody else added data to the file between your seek and your write.

