---
layout: page
title: HowToOpenAFileUsingCStdLib
---

See also the related discussion in OpeningDataFileInAuxiliaryExecutable

I have a resource file that I am using fopen() to open.
It works fine when running the program directly from  Xcode, but cannot find the file when the application is run by itself.

----

If you use a relative path, your current working directory may be different when run within Xcode and when run directly.  Use the getcwd() call to see what your current directory is in both cases.

----

When the application is launched from the GUI, the CWD is just my home folder instead of the application folder itself. I could use chdir() to set an absolute path, but how do I change the directory to a path relative to the application itself? 

----

try either of these:

    

NSString *executablePath = [[NSBundle mainBundle] executablePath];
NSString *bundlePath = [[NSBundle mainBundle] bundlePath];



Specifically, something like     [[[NSBundle mainBundle] bundlePath] stringByAppendingString:@"path/to/whatever.file"]

----

It's better to use something like     [[[[NSBundle mainBundle] bundlePath] stringByAppendingPathComponent:@"path/to/whatever.file"] stringByStandardizingPath] rather than appending the path as a string. Always, always use the NSString-provided path methods when dealing with paths. There's no reason not to.

When using NSBundle and resource files specifically,     [[NSBundle mainBundle] pathForResource:@"whatever" ofType:@"file"] is probably best.

