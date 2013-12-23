---
layout: page
title: SettingMetadata
---

What is the preferred manner of setting metadata tags on files already in the filesystem?  I would like to go through the files in a directory and set their kMDItemCreator and kMDItemFinderComment values, for example.  I don't see why I would write a whole spotlight importer to do this, especially since the metadata isn't going to be derived from file content, but rather set at run time.  I've been looking at NSMetadataItem and MDItem and can only find methods for getting the attributes, not setting them.  I must be really missing the boat on this.

----
kMDItemCreator corresponds to the HFS creator code, so just set that using traditional methods. (NSFileManager can do it.) From what I've read, kMDItemFinderComment is stored in mysterious places and the only real way to set it programmatically is by AppleScript<nowiki/>ing the Finder, which sucks.

----
What about the 100 or so standard tags listed like kMDItemLastUsedDate, or kMDItemStarRating?  These last two are tags which the user is likely to change randomly and often, and I don't understand how an application other than the creator (who creates an importer) is supposed to set these attributes.  Is there even a setValue:forKey: method for the files themselves?  Maybe I'm not clear on the role of the importer but that just doesn't seem to fulfill the role in question...

----
While you can update file metadata from an application I would suggest take care... If the files are changed by anything later then the normal metadata importers will run and automatically delete/overwrite (most/all?) existing metadata on the file.

----
The mysterious place you talked about seems to be .DS_Store file of the file's parent folder...
Apple didn't gave any specifications on .DS_Store files.

----
The problem is that Spotlight (MDItem/NSMetadataItem) doesn't know how to set metadata properties in files. For example, Spotlight doesn't know anything about how the kMDItemExposureTimeSeconds is derived from an image or how to write that information into an image, it just believes whatever the importer tells it. The same goes for almost all of the other values -- and it doesn't make sense to change the value of some metadata in the Spotlight store, but not in the file.

You'll have to read the files, update the values, save them back out and let the importer capture the changes.
----
*I don't understand how an application other than the creator (who creates an importer) is supposed to set these attributes.*

I think you answered your own question with the line above. An application other than the file creator is generally NOT supposed to set metadata attributes on a file. That's why, for example, mdimport only runs one metadata importer per file. I'd guess, from the questions you're asking, that you're hoping to be able to store some metadata of your own for arbitrary files so that you can add some sort of feature to the system. You could do that if mdimport would just run *all* the importers that match a file rather than just the best match.

I talked to some of the Spotlight guys about these issues a couple years ago at WWDC. They were clearly aware of developers' desire for that sort of behavior, but made it clear that that's not, at least right now, how Spotlight is intended to be used. And their position is very understandable... if mdimport ran just two importers per file rather than one, Spotlight indexing performance would suffer significantly. Given that many developers want this sort of feature so that they can add data to any file, there's a good chance that any given system might have three or four or ten importers that would want a crack at every file on the system. I'm sure you can see how that would really slow things down. -CS


----
what does one do about the case in which your app is the creator of the file?  as someone wrote above, it seems silly in a way to provide an importer for filetypes your app can create, especially if they're generic filetypes which could conceivably be created by other apps on the system (and for which importers may already exist).  for example, if i have a program which processes email messages and extracts attachments ... my program is creating the actual files by parsing and decoding the various mime parts of a message.  i want to be able to set kMDItemContentTypeTree or kMDItemContentType for the files i create.  i don't want to have to rely on file extensions (wtf was apple thinking when they adopted that crap?!) or type/creator info either.  it'd be nice to use this new uti stuff, but ... 

does anyone know a way to handle this seemingly simple case from within objc?

(EDIT ... later ...)  hmm, it looks like setxattr (2) may be what we're looking for here:
    
setxattr, fsetxattr -- set an extended attribute value

SYNOPSIS
     #include <sys/xattr.h>

     int
     setxattr(const char *path, const char *name, void *value, size_t size, u_int32_t position, int options);

     int
     fsetxattr(int fd, const char *name, void *value, size_t size, u_int32_t position, int options);


----
xattrs aren't metadata (in the spotlight sense).  Though as an aside - Finder comments are stored as a com.apple.metadata:kMDItemFinderComment xattr, but that's just Finder playing games - RbrtPntn

----
thanks, i just realized that the xattrs supported by hfs+ (since tiger i guess) and this kMDItemWhatever stuff which is apparently where the UTI stuff actually lives are two entirely different things after beating my head against it for a couple days.  however, i still can't believe there's not a (relatively) easy way to set a damn UTI so you can have some control over what app is going to open a document you create, without having to resort to the retardedness of file extensions or the (what i've (mistakenly?) been assuming to be deprecated) old-school type & creator codes ...

----
Spotlight importers import metadata for one (or a group) of file types. But Apple has a system to import metadata on any file: if you set an xattr extended attribute with a plist that is not a dictionary, under a key like com.apple.metadata:YourKeyNameHere then in the spotlight database you will find an entry under 'YourKeyNameHere'. (you see them by doing an mdls on the file). We have developed OpenMeta to come up with a standard set of keys and safe procedures for setting this data. Apple itself uses this feature all over the place, with kMDItemWhereFroms, with time machine, and many other places. You can also use this system to for instance set a GPS coordinate on a text file, or a camera model on a PDF. These xattrs stay with the file through editing, (there are some Adobe apps that have a bug that erases xattrs at every save though!)

By creating a spotlight plugin, a search for 'tag:superman' in the Spotlight UI will find all files with a com.apple.metadata:kOMUserTags array with 'Superman' as an array element. 

See http://code.google.com/p/openmeta

