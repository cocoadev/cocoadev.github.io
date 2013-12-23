---
layout: page
title: ExpandingTildeVsStandardizingPath
---

Isn't it better to use     stringByStandardizingPath than     stringByExpandingTildeInPath? The former will follow symlinks and the latter won't, but I always see     stringByExpandingTildeInPath and hardly ever the other one. How come?


Here's Apple's docs on each:



**- (NSString *)stringByExpandingTildeInPath**

Returns a string made by expanding the initial component, if it begins with "~" or "~user", to its full path value. Returns the receiver unaltered if that component can't be expanded.


**- (NSString *)stringByStandardizingPath**

Returns a string representing the receiving path, with extraneous path components removed. If stringByStandardizingPath detects symbolic links in a pathname, the stringByResolvingSymlinksInPath method is called to resolve them. If an invalid pathname is provided, stringByStandardizingPath may attempt to resolve it by calling stringByResolvingSymlinksInPath, and the results are undefined. If any other kind of error is encountered (such as a path component not existing), self is returned.

This method can make the following changes in the provided string:

    * Expand an initial tilde expression using stringByExpandingTildeInPath.

    * Reduce empty components and references to the current directory (that is, the sequences "//" and "/./") to single path separators.

    * In absolute paths only, resolve references to the parent directory (that is, the component "..") to the real parent directory if possible using stringByResolvingSymlinksInPath, which consults the file system to resolve each potential symbolic link.

      In relative paths, because symbolic links can't be resolved, references to the parent directory are left in place.

    * Remove an initial component of "/private" from the path if the result still indicates an existing file or directory (checked by consulting the file system).

----
I think most people use     stringByExpandingTildeInPath because they are trying to find a directory within a user's home directory. Rather than perform the less clear     [NSHomeDirectory() stringByAppendingPathComponent:@"Library/Preferences"] (for example), they can just use the "simple" tilde expansion:     [@"~/Library/Preferences" stringByExpandingTildeInPath], although this function is probably implemented the same way (and is likely less efficient). Basically, for almost any preprogrammed or behind-the-scenes value, people prefer the "expanding tilde" method. Also note that paths are probably standardized automatically by almost any file IO method or function (although I have no test for this). --JediKnil

*but what if the preferences directory is a symlink? wont just expanding the tilde clobber it? has anyone tested this out?*

----

No, the kernel's VFS will still have to honour symlinks.  The only reason why you should need to expand the symlinks is if you want to use the path elsewhere, to show the user where the path physically points to.  Realistically, though, that isn't particularly useful.

