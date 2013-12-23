---
layout: page
title: AreResourceForksObsolete
---

Hi there, I am saving plain text files and so, I cannot store any meta data for these files in the file itself.

Is there any reason to favor a central repository for file metadata over resource forks?

I realise that resource forks will not be respected by cvs, ssh/scp or other unix commands which may work with the files, but OTOH, if some other program mutate the file contents, then the metadata might no longer be valid.

----

The problem with resource forks is now, almost 20 years later, the other 98% of the computing world still doesn't know what they are and how to deal with them. Take a file that has critical data stored half in data and half in the resource fork. FTP that file to Windows. Oops, now you can throw the file away. Or 'cp' it from Terminal. Oops, shouldn't have done that either. Drag it into Mail.app and send it to your non-Mac-using-friend. Oops, it's not your lucky day.

Add to that resource forks are a filesystem-specific feature, and HFS is just one of many, many file systems out there. Sure, CarbonCore does its best to emulate resource forks by creating many turd files on non HFS file systems. But CarbonCore only runs on Mac OS X ... not that other 98%.

So clearly, resource forks have problems. They're an idea whose time has come ... and gone. 

But the feature does exist on Mac OS X. **The basic thrust coming out of Apple these days is resource forks (and HFS-specific file metadata, like types/creators) can be used for non-essential stuff** -- stuff where it's no big deal if the data is lost (as in the cases above). For example, custom document icons like image thumbnails, labels, document visibility, and so on. 

And resource forks will probably always remain supported for compatibility reasons. Some things, like really old plugin models, really old drivers, CFM binaries, and really old fonts, simply require HFS-style resource forks. If the resource fork is missing these files don't work. there's no assurance some programmer (now, in the future, or 15 years ago) isn't going to put meaningful data in a resource fork somewhere. So as a general rule you can't just strip all your resource fork data away.

So to answer your question directly, if you're just shoving some ephemeral data in there that doesn't matter (window position, a font override, custom icon) a resource fork is a fine best choice. If you're storing data that needs to accompany your text file and without it the information in the text file is meaningless or damaged, pick a new file format: perhaps a structured file, a pair of files, or a directory bundle.  

I'm not a big fan of the "central directory" (ala Desktop Database). They tend to get cluttered with junk. Imagine storing text file window positions for ever file you open in your app's user defaults. Your defaults will grow without bounds as you open files -- even if you delete the file when you're done. That information is best stored with the file directly.

But central repositories can serve a good purpose, especially as a data cache. I.e., rather than scanning every file on the system every time you need some data, you can build a central repository and consult it as needed. Just remember that the cache is just that -- a cache -- and the "real data" actually lives elsewhere. That means you'll need to know how and when to resolve differences between the cache and the "real data". 

HTH

-- MikeTrent

----

Mike, I removed the MacOS 9 reference from the mention of the desktop database, it always *was* a cache, and it was automatically rebuilt if it got deleted or damaged. Just like today's LaunchServices cache files, which have taken over the role of the desktop database, after all. -- UliKusterer

As I recall, Desktop Database was notorious for developing stale information and corrupting itself. It was not automatically rebuilt when corrupted, nor did the system remove files from the database when they were missing. So I suppose I'm claiming it was a buggy cache. -- MikeTrent

I don't remember having a corrupted DD, ever. Though I *did* have to delete the LS cache a number of times. But you're right, the DD did have the habit of accumulating cruft, which was especially annoying on small volumes like floppies. So I guess a general warning against keeping such data separate from documents without some scheme that cleans up old stuff would be in order. -- UliKusterer

