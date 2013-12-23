---
layout: page
title: PasteFilesToMyApp
---

I am eager to know how to get the file names in NSPasteboard

1) Copy more than one files in Finder
2) In my Cocoa Application, I need to get these file names through NSFilenamesPboardType but I failed.

Does any one here know how to get these information about the files in NSPboard?

Thanks

---- 

are you reading with

    
[[NSPasteboard generalPasteboard] propertyListForType: NSFilenamesPboardType]

? See [http://developer.apple.com/documentation/Cocoa/Conceptual/CopyandPaste/Concepts/DataTypes.html]

----

Apparently you're not the only one who's run into this- see [http://cocoa.mamasam.com/COCOADEV/2002/09/2/46160.php]. Sorry I don't have a solution, but at least you're not alone.

----

It looks like you are out of luck if you need the full paths to the items copied from the finder. The only types posted to the general pasteboard when multiple items are selected and copied in the finder are:

    
    "CorePasteboardFlavorType 0x75747874", 
    NSStringPboardType, 
    "CorePasteboardFlavorType 0x54455854", 
    "CorePasteboardFlavorType 0xC46C7374", 
    "CorePasteboardFlavorType 0x66636363"


http://www.bekkoame.ne.jp/~n-iyanag/researchTools/clip_utils_osx.html <- talks about the CorePasteboardFlavorType

If you ask for:

    
 [generalPboard stringForType:NSStringPboardType];


you get a string with just filenames, not full paths. 

You can get full paths by registering a view for the dragAndDrop of filenames (DragAndDropWithNSViewSubclass):

----

The translation of those flavor types is 'utxt' (Unicode text), 'TEXT' (plain text), '�lst' (file list?), and 'fccc' (?).

