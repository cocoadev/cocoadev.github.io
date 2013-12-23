---
layout: page
title: TerminalSlashToMacColon
---

In the terminal, you provide a file via whatever/whatever, right? But the "true" macintosh, IE not the terminal, if you output a filepath gotten through cocoa, it will use : instead of /?
Can someone provide conformation on this, and provide a solution for foundation programs that use file-path arguments?

It is my opinion that you have just stepped out of a time machine from 1999.  To expand on that, colons are out -- they have been ever since OS X came out.  The General/NSString methods that deal with paths all rely on paths being separated by a slash.

The colons exist only on the kernel level.  When the files are written to disk, the kernel swaps slashes for colons.  Hence in OS X both slashes and colons are illegal in filenames.  For everything that's not kernel work and that does not try to access the filesystem directly, use slashes.  --General/OwenAnderson

----

Slashes and colons are not illegal in filenames. Internally, one or the other is allowed, I forget which. The General/APIs will translate the slash to a colon in places where slash is used as a path separator.

The solution is basically, "don't worry about it". Forget the problem was ever there, and let the system do its thing.

----

My understanding (which may very well be incorrect) is that only Carbon apps use colons, and that the system uses slashes. After all, OS X is based on Darwin, which is slash-friendly. I would wager the kernel doesn't know or care, the filesystem API handles it, and since Darwin has all that low-level stuff, my conclusion is that slashes are the official path separator.

Now, the finder uses colons. But the finder is a mess. Ported carbon apps, like Flash, use colons too...

I would assume that Carbon has a translation system, probably somewhere it translates colons to slashes, or a global API function lets the developer say "I'm using slashes" or "I'm using colons" and then the CF path utils translate accordingly. But as far as I'm concerned, the Carbon API is worse than GTK+ programming, and the documentation is terse and hard to navigate and assumes you already know. So, I'd stay away from this bag of snakes.

If you stick to cocoa and the standard posix hoo hah ( like fopen ) you'll be fine dealing with forward slashes.

--General/ShamylZakariya

----

The path separator is a property of the filesystem, not any API. HFS+ uses colons to separate paths, so it's Cocoa and the posix stuff that's doing the translating. But I still expect to see paths separated by slashes on OSX, regardless of filesystem or API.

*I always thought the path separator was in the General/APIs. Filesystems don't even have the concept of a path, much less a path separator. After all, a FAT32 volume will use : as the separator on Mac OS 9, / as the separator on Linux, and of course \ as the separator on Windows.*

----

I don't pretend to be an expert on filesystems, but [http://developer.apple.com/documentation/General/MacOSX/Conceptual/General/BPFileSystem/Concepts/Comparisons.html] says:

*HFS+ uses colons as path separators whereas UFS follows the convention of forward slashes. The system translates between these separators.*

It's possible the file viewers on the various platforms are performing the translation themselves. Also, Windows can accept either / or \ in various situations.

I remember there being a fairly lengthy discussion on exactly what's going on here in one of the OSX articles at arstechnica.

----

If I'm not mistaken, General/AppleScript can't work with slashes at all. [Well, perhaps that's a misstatement, since General/AppleScript itself doesn't have any real file-handling abilities, but works with other apps like the Finder (Carbon) or additions like Standard Additions (also Carbon, I think). Anyway, using General/AppleScript with the Finder to do any file operations requires colon-based, Classic-style paths.] So what happens when I need to use General/AppleScripts from my Cocoa app? If I want to pass an General/AppleScript a file as a parameter (or have it return a file), how should I go about the conversion? Is it as simple as replacing slashes with colons and colons with slashes? I don't want to implement a hacky solution and then later discover that it blows up under some rare cirumstance.

----

Check Standard Additions / Misc. Commands / Classes / POSIX file. It allows you to create a file object using a POSIX (slash-separated) path.
