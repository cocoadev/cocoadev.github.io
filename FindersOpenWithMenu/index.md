---
layout: page
title: FindersOpenWithMenu
---

For some reason I cannot make my applications appear in Finder's Open With menu. I can select Other... and choose my application (so the document type settings in the Info.plist seems to be correct), but even after this, the choice of my application is not present in the menu the next time I open it.

I have moved my application to /Applications and restarted the machine, but this have not helped (and it would seem that Finder does cache open applications, as I get e.g. World Text as an option for text files, which is in a /Developer sub directory).

Does anyone know if there is a cache that I need to delete or if I need to provide other information in my Info.plist than what can be edited in Xcode's Document Types? For the records, I currently have this in my Info.plist:
    
    CFBundleDocumentTypes = (
        {
            CFBundleTypeExtensions = ("*");
            CFBundleTypeMIMETypes = ("text/*");
            CFBundleTypeName = NSFilenamesPboardType;
            CFBundleTypeOSTypes = ("****'");
            CFBundleTypeRole = Editor;
        },
        {
            CFBundleTypeExtensions = (html, htm);
            CFBundleTypeMIMETypes = ("text/html");
            CFBundleTypeName = "HTML document";
            CFBundleTypeOSTypes = (HTML);
            CFBundleTypeRole = Editor;
        },
        {
            CFBundleTypeExtensions = (txt, text);
            CFBundleTypeMIMETypes = ("text/plain");
            CFBundleTypeName = "Plain text document";
            CFBundleTypeOSTypes = (TEXT);
            CFBundleTypeRole = Editor;
        }
    );

I have also provided my Info.plist with a (unique)     CFBundleIdentifier and a     CFBundleSignature (and provided the various version strings).

----

One thing to try is moving the generic file types declaration after the other types. HTH.

----

Thanks a lot!!! That did the trick -- I actually did consider it once, but thought, no that can't be it... you have saved my day! :)

One problem though, but that is with the OS design: it then only is an option for the html and text files -- this is a text editor, so it really needs to be there for a lot of different file extensions, and I would then have to enter them all in the Info.plist. Likewise I have written a hex editor which works on all files w/o exception, and I really would like to have it in the Other... menu, but that then seems to be impossible?

�I think it is impossible. There is no other application I've seen that appears in every *Open With* menu. But if you want it to be available from the *Other�* app chooser dialog, look at how TextEdit declares its file types; it is always available.

----

I'm trying to allow dragging ZIP files onto my application's icon in the dock BUT I don't want it to show up in the 'Open With...' menu when right-clicking a ZIP file. 
The reason is that the type of files that my application accepts are more commonly found compressed in ZIP files. It will then extract the ZIP and keep the files inside.

My application is not a ZIP decompression utility therefore I do not want to mislead users who right-click a random ZIP into thinking that my app will decompress it.

Short of allowing ALL file formats to be accepted by my application, I see no way to do this.

Does anyone know how this could be done?

