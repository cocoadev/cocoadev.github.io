---
layout: page
title: MessageSentOnDropOnDockIcon
---

This is just a curiosity of mine, it's not actually relevant ATM to what I'm working on. But, anyway, I'm curious how Mac OS X signals to a cocoa app that the user wants it to open a file by dropping a file onto either the app's dock icon or the app icon in the finder ( both while the app's running and when it's not running ) or double clicking the file if it's associated to your app.

I'm just curious since I've developed for several toolkits before finally coming to Cocoa. In BeOS, you could parse command line args, but you could also trust in the application object receiving a standard "open" type message with parameters describing the file list dropped -- this would always occur, wether the app was started by a drop event or if the app was running when it occurred. Other toolkits, like Qt on linux, just had command-line argument token parsers, albeit good ones.

The app  I'm currently working on might benefit from having it's (very custom) file format be associated to it in the plist, and as such, it would make sense to have it open the app on a double click on the file.

The trouble is, I've read quite a bit of the Cocoa documentation and I haven't a clue where to find this tidbit. For what it's worth, I'm sure this comes for free with a Document based app, but mine isn't...

Can you point me to some documentation on this topic? I expected to find it as a notification/callback for NSApplication's delegate. 

*which is precisely where it is:*  **application:openFile:** *is a NSApplication delegate message. Panther adds an openFiles method when multiple files have been opened at once. See also * [http://www.macdevcenter.com/pub/a/mac/2002/06/14/cocoa.html]

"beats me how I missed it. Thanks!"

... another thing... the custom file format I'm using is more like a custom bundle format. It's a folder with an extension containing several xml files, binaries (shared libs for custom plugin logic) images, etc. I've got my open dialog successfully picking the folders (and letting the app grab the files therein), but what I'd really like is to be able to associate folders with that extension to my app. Several apple apps do this already ( PB, XCode, nib files, I think iMovie as well ) but what I'm wondering is if having folders be "filetypes" or registered "bundle types" is Apple-only magic, or if we can do that ourselves. And if so, what are the the requisites?

--ShamylZakariya

*in the Document Types section of your Info.plist, check the File Package checkbox.* You might need to logout and/or restart for Finder to recognize your format as a bundle.

----

Look at the TextEdit source at /Developer/Examples/AppKit/ - it's a non NSDocument based app that handles file drag & drop and file packages (RTFD)

Thanks. I appreciate the tip, but I'm no fan of RTF* responses; it's just not nice, particularly when I've spent quite some time reading documentation. --ShamylZakariya

I'm not a fan of them either, but in this case RTFD refers to the *file format. ;)* Drag an image into a TextEdit document and save it. It will be in RTFD format, which is a file package containing the text portion as rtf, plus the included images in the directory. I only meant to point you to the particular entry in TextEdit's doc types listing you should look at.

Arg! My bad. Thanks, again! --ShamylZakariya

