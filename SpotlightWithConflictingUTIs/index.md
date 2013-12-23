---
layout: page
title: SpotlightWithConflictingUTIs
---



I'm writing a Spotlight importer for an application. The program plays sound files in a special format. The files all have the extension .snd

My problem is that QuickTime has already registered an UTI for files with the .snd extension. They are ULAW audio files. This prevents my Spotlight importer from picking up the .snd files. If I change my UTI to include some other (unused) file extension, then everything works the way it should.

How an I supposed to deal with this? It must be a pretty common occurrence that there are more than one UTI claiming the same extension. I'm at a complete loss here and I have no idea how to proceed. Any suggestions would be appreciated.

Here's the UTI specification for my file type:

    
<key>UTExportedTypeDeclarations</key>
<array>
        <dict>
                <key>UTTypeIdentifier</key>
                <string>org.atari.sndh</string>
                <key>UTTypeIconName</key>
                <string>sndh_file_type</string>
                <key>UTTypeReferenceURL</key>
                <string>http://xsc.atari.org/</string>
                <key>UTTypeDescription</key>
                <string>Atari SNDH File Format</string>
                <key>UTTypeConformsTo</key>
                <array>
                        <string>public.audio</string>
                        <string>public.data</string>
                </array>
                <key>UTTypeTagSpecification</key>
                <dict>
                        <key>public.filename-extension</key>
                        <array>
                                <string>atarisnd</string>
                                <string>snd</string>
                                <string>sc68</string>
                        </array>
                </dict>
        </dict>
</array>


-- EliasMartenson

----

You can't redefine an extension, at least as far as I'm aware.  What you will probably need to do is remove the .snd declaration from your UTI, import the public.ulaw-audio UTI, and declare that your app can open it.

----

First a correction: The ULAW file type is not claimed by QuickTime as I wrote above, but rather by Audio.mdimporter. All other facts remain.

The problem isn't that I can't open it (which I can, since the application itself also specifies handles types using the old format, which makes it pick up the .snd files). The problem I'm having is that the Spotlight plugin won't pick up the .snd files, since they are already claimed by Audio.mdimporter as being ULAW.

UTI becomes kind of useless if one has to fake the UTI just because the extensions conflict? One of the major points of UTI was, if I understood correctly, to overcome the problems of conflicting extensions.

There is a document in the Apple documentation that deals with UTI problems with Spotlight:

http://developer.apple.com/documentation/Carbon/Conceptual/MDImporters/Concepts/Troubleshooting.html#//apple_ref/doc/uid/TP40001690-222978

As far as I can tell, I've covered everything in this document. It does seem that the problem is that both UTI's declare the same file extension. The question is if the is a solution to the problem? It's not reasonable to ask the users to delete or modify Audio.mdimporter.

-- EliasMartenson

----

If your sound file opens in your application if you double-click it in Finder, then I'd consider it a bug for LS not to know its proper UIT. That is, if your application (not the importer) accurately defines the uti in its document types. (LS should be able to reason: this is a .snd file the user would open with AtariPlayer -> AtariPlayer .snd documents have the uti "whatever" -> I should use the "whatever" importer to import those).

----

Can't you just associate a unique MIME type or Creator/Resource code with your snd documents and use that to distinguish your UTI?  I don't have any direct experience with these issues, beyond reading about them in places like this, but Apple's table of system-defined UTIs at 

http://developer.apple.com/documentation/Carbon/Conceptual/understanding_utis/utilist/chapter_4_section_1.html

suggests that a UTI could be associated with MIME types and creator/resource codes, as well as with file extensions.  There must be plenty of examples of different file types that use the same extensions.  Even in the UTI table I referenced above, I notice that ".aiff" is an accepted file extension for both AIFF and AIFF-C audio files, which have distinct UTI's ("public.aiff-audio" and "public.aifc-audio", respecively).

-- TomPollard

