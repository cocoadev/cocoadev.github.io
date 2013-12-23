---
layout: page
title: PlugIns
---



----
**Info on the web:**


*CocoaDevCentral: http://www.cocoadevcentral.com/articles/000068.php
*MacDevCenter: http://www.macdevcenter.com/pub/a/mac/2002/10/15/cocoa.html
*Apple: http://developer.apple.com/documentation/Cocoa/Conceptual/LoadingCode/Tasks/UsingPlugins.html
*'Hack' plug-ins: http://www.stone.com/The_Cocoa_Files/Writing_PlugIns.html


----
**Pages on this wiki:**
[Topic]

To add a relevant page, put \\%\\%BEGINENTRY\\%\\%PlugIns\\%\\%ENDENTRY\\%\\% anywhere on the page you wish to add.
You do **not** need to edit *this* page.


*NSBundle
* LetingOthersChangeTheProject
*TextExtras
*FScriptAnywhere


----
**Why plug-ins?**

----
**How to...**

There are ways to provide plug-ins for application that have not been designed to accept plug-ins. I guess these could be called 'hacks', but can still be quite clean. They will usually break with new versions of the main app. Such hacks have been developed for Safari, iPhoto, etc... (see one of the link above).

But you can also have applications that are designed to accept plug-ins, and this can usually be done in a less brutal way. How to prepare your own application for this?

According to Apple and the different tutorials around here, there are 2 different ways to implement a plug-in architecture into your app. This depends on the way you want to provide the API to give other people (or you) the possibility to develop such plug-ins. It also supposes your plug-ins will be bundles, i.e. folders organized in a very specific way, defined by Apple, containing code, and that can easily be loaded in Cocoa (see NSBundle). The plug-in is loaded by the main app, which then talks to the plug-in a way defined in advance, usually through one specific class. Here are the options:


*Case 1: the principal class of the plug-in follows a formal protocol given in a header file provided by the developer of the main app.

This seems to be the easiest way, and is yet powerful. The tutorial at CocoaDevCentral covers that case pretty well (see link above).

*Case 2: The principal class of the plug-in is a subclass of an abstract superclass.

The compiled code for this superclass is provided by the developer of the main app though a framework (which includes the header file of the superclass), which can be linked to the plug-in.

This approach appears to be a little more complicated to implement, but may be useful and/or necessary to provide methods to the subclasses in the plug-in, so they have less code to write. One of the difficulty is to let the plug-in know about a class without the source code (the superclass developed by the author of the main app). The header is not enough. This is where providing a framework can help and this is what is suggested by Apple on their introduction to plug-ins.

*The header is enough if you use the right linker flags.  Try     -framework Foundation -framework AppKit -force_flat_namespace -undefined suppress.*

*Or take advantage of the     -bundle_loader flag to make the application be your "framework":     -bundle_loader /path/to/your/Whatever.app/Contents/MacOS/Whatever*

Unfortunately, there does not seem to be any full working example/tutorial on how to do that exactly. The excellent MacDevCenter tutorial shows how to do it for plug-ins bundle inside the app, for which the developer does not need to build a separate framework and does not have to deal with linking issues. The Apple page has some code, but no clear directions on how to fully implement the thing. One interesting point made in the Apple code is to leave room for future addition of instance variables to the superclass, which will ensure backward compatibility with plug-ins developed with older versions of the plug-in framework (this is where you remember that objects are pointers to C struct...).

There are however other tutorial around on how to build a framework. Here, the framework to build would probably just have one class, the abstract superclass that plug-ins are supposed to subclass. Remember to make the header of that class 'public'. The developer writing the plug-in would then have to link against that framework, and #import the header in the subclasses headers.

Where and when to install the framework? 

[Ronald Oussoren: in 10.3 or later it is possible to use     -undefined dynamic_lookup, which causes symbol lookup to occur at runtime (for symbols that weren't found at link time), instead of     -force_flat_namespace -undefined suppress.]



----
**Example of Cocoa plug-ins SDK/API**

What interface others have proposed to 3rd parties developers interested in developing plug-ins for their app:

*XGrid -Technology Preview 1 and 2- (subclass approach)
*4Peaks by MekenTosj: http://www.mekentosj.com/4peaks/ (protocol approach) - Apple Design Award WWDC 2004
*Data Bank by SpectralClass:  http://www.spectralclass.com/developers.php?link=opensource (direct download:  http://www.spectralclass.com/downloads/data_bank.dmg.gz ).  Uses the framework approach for code sharing through inheritance.  Not the most useful app but it does demonstrate how to reuse code, effectively, across plugins and the host application.

