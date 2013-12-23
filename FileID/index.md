---
layout: page
title: FileID
---

A FileID is a number HFS+ uses to index a file. FileIDs are thus equivalent to a file path on a UNIX file system, while the path on a HFS+ volume is more like a file attribute.

There are several advantages to FileIDs as opposed to file paths. The main advantage is when referencing a file or folder by it's ID, you don't lose track of it when the path changes. This has human interface implications.

Another advantage is performing various filesystem operations like searching for files is faster since you only have to deal with FileIDs and host FolderIDs instead of long path names. 

A disadvantage is other filesystems don't support FileIDs, which is why you should use Aliases and FSRef instead. 

-written by PaulBayley

According to Apple, in Snow Leopard:

"In addition, you can now create two kinds of file: scheme URLs�path-based and file-ID based�so you no longer need to rely on paths when files are moving around or directories have been renamed. File-ID based URLs contain the volume ID and file ID of the target, and can be used to specify a file or directory (or a bundle, which is a directory by another name)."

This will hopefully help matters somewhat. It isn't clear to me what happens when using this type of file object on a filesystem that doesn't support FileIDs. Perhaps someone with Snow Leopard can find out (I don't have a x86 Mac yet). Apple's documentation on this feature seems a bit lacking, but they're making it clear that only URLs are to be used. What they aren't making at all clear is which kind is preferred. 

----
Since file-ID based file URL<nowiki/>s are supposed to replace aliases ("bookmarks" when using them this way) and FSR<nowiki/>efs, I imagine they handle other filesystems somehow. Or perhaps they just drop down to path-based URL<nowiki/>s (an alias on a non-file-ID volume is basically a resource-fork symlink).

As for which one to use, it depends whether the path or the contents is more important. So, NSDocument uses file ID<nowiki/>s, so they can tell when the file moves. Doing any kind of relative path operations requires file paths. The nice thing, though, is that conversion is transparent, so it's only when storing file URL<nowiki/>s that you have to care what kind you have. (I suppose that could include URL<nowiki/>s that persist past a single event event loop, since the user could change something in the meantime. URL<nowiki/>s within a single method are probably safe enough.)

