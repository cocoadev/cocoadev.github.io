---
layout: page
title: ApplicationSettingsAndDocumentTypes
---



I have finished my Cocoa app (not NSDocument based), and now wish to set up the document icons etc. and have my app launch when its documents are clicked etc. 

I have been trying to get this to work in XCode by clicking the output target and going to Project>Get Info

The Icon File for the Application itself works fine, I have the 'Executable' and 'Identifier' down as the same thing, the name of my App.

Type is APPL (this is Application right?)

Creator... well I don't know what this is. At first it was ???? and things didn;t work so I changed it to 4 letters to see if this was any better... but no.

I have 1 document type, I have given it a name "MyApp Document"

Class I have left blank

Extensions is simply 'myap'

MIME types is blank.

Iconfile is the same as the Icon file for the application.

Role is editor.

Package is unchecked.

Yet no matter how many times I move the app into the Application directory (on advice of some) or log in and out, nothing changes. I save files, but they are just saved as generic document files without my icon.

I have cleaned the project several times.

Getting Info on the documents in the Finder shows up nothing much... it is of Kind: Document, it has no extension (this is the problem?) and Finder does not know what to open the file with (Open with: None).

- P

----

The creator code is important, this is four letters which uniquely identify your application -- it should be allocated from Apple (they have a webform somewhere).

That said, typing in random letters will do for your testing -- but your documents *must* have an extension, otherwise it will not work. And the extension should be the one entered in the document type settings, i.e. 'myap' as you've quoted above (which is a weird extension IMHO).

There is one alternative, that is to set the creator code for the file (each time you save it), but this is really an obsolete Carbon-thingie of the past (since it relies on metadata not available on all filesystems nor when the document is sent over the internet).

----

The identifier should be a valid identifier, e.g. com.mycompany.myapplicationname. If you have a web site, you can use that in reverse-notation, i.e. for www.zathras.de that would be de.zathras.myappname

If you're missing the icons, several things to check: Are your icons valid .icns icon image files? Do they have the .icns suffix? Have you added them to your project? Were they actually copied into your app's "Resources" folder? Also make sure uppercase/lowercase characters are the same. This will probably not bite you today, but may in the future. Also, the file needs to have either a name extension or an OS Type to get an icon, because otherwise the OS will refuse to make assumptions which document type this is supposed to be.

You don't need a creator yet. Creators are good if your program edits some standard file format (like .txt, .png or whatever) and you want to give your users the option of having files created with your app automatically open with your app instead of the default app for that type. In that case you'd register a creator at Apple's type/creator registry and then assign that to all documents you save (by returning that from fileAttributesToWriteToFile:ofType:).

Similarly for OS Type, just that you don't register those anymore (though be careful, because some other app may already be using it, and then you're in hell's kitchen), and that they're there to allow the user to save your file type without a name extension and still have it recognized as being a particular type. This could e.g. be useful for a Makefile editor, but of course most makefiles come from Unix where they don't have OS Types, which means you'd need some other way for opening it the first time.

----

If you're still having problems, see MakingIconsAppearInFinder.

