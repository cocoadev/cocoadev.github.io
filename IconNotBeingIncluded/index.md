---
layout: page
title: IconNotBeingIncluded
---

See Also:

* 
* MakingIconsAppearInFinder


I only recently started working with XCode 2.1 and have been going through the process of having it update my old projects with the new .xcodeproj file it wants to make. However, one of my projects no longer wants to include its icon. The icon file is in the main folder of the project, and is included in the project in Xcode, listed under Resources. If I double-click on the target and go to Properties, the name of the icon file is filled in in the Icon File field and the correct icon appears next to it. As well, the icon appears correctly if I choose About <application> from the Apple Menu while running the app. But the icon doesn't appear with the app in the Finder or in the Dock; the generic application icon appears instead. Has XCode 2.1 changed where icon files need to be?

----

**Possible Causes**


*Finder has not yet recognized your application's icon. See: MakingIconsAppearInFinder
*The icon file's Target Membership checkbox is unchecked for your app's target (which excludes the file from being copied into the target application's bundle - see below).
*Your target simply needs cleaned and re-built (See XCode help).
*The ICNS file, while seeming OK, might not be such as OS X wants it. Check with "Icon Composer" in the Developer Tools.


----
**Target Membership**

"Target Membership" refers to a file's inclusion when building a specific target in your project. To see a file's target membership(s) in XCode 2 or above, click the file in your project and press Cmd-I. Click the Targets tab. If you want your file to be included in the target(s) listed in the Target Memberships list, make sure that target is checked in the list. If it is not checked, resource files will not be copied into the application bundle and source files will not be compiled and linked into the executable.

----

**Icon with transparency looks OK in About window but does not represent app in Dock**

I created an icon in Photoshop. Usually, I make square icons but this time the icon is better suited to an odd shape. So, I made it in Photoshop on a transparent background, then exported it via 'Save For Web...' and chose the PNG-24 option. Then, dragged it into Icon Composer, etc.

Long story short, I bring the icns into the Project and add it to the Target. Looks fine in the target's Properties windows. Also looks fine in the app's About window. However, I'm still getting the generic icon in the Dock when launched, in the Finder, and in saved documents from the app (which is using the same icon).

I've already cleaned the target and restarted the computer, but I'm getting the same results. If I take the same Photoshop doc, but use 'Save for Web...' and choose JPEG, the icon appears fine (though exporting as a JPEG adds a white background to the whole icon).

----

You need to use the icns format for your app's icon. You should be able to export it using Icon Composer.

----

I used Icon Composer. Once I exported the PNG from Photoshop, I pulled up Icon Composer and dragged the file into each of Icon Composer's 4 icon sizes, then saved the icns file and used it in the project.

(*Editor's note: never resolved*)

----

I did a quick experiment with several versions of a new doc-based app I am working on. Built the icns file from a png image with transparent regions. Added it to project. Specified the icns file in the target inspector, without typing in its extension. Built and ran. Application icon was installed correctly, but the only way I could get the icon to attach to the document was by specifying the .icns extension in the document types area of the target inspector. By the way, I do not know the application that created the original png image I am using for this experiment. HTH.

----

You shouldn't have 'several versions' of your app around - you'll confuse Launch Services. Delete all but one (really delete - move to trash, empty trash. Leaving the trash full won't work) then drag the icon file into the image well in the document types pane and logout.

----

Thanks for the heads-up on that. Generally, I adhere to this advice when working with several versions. And certainly did so in the above experiment. Which turns out to be a really good advertisement for turning off indexing in your projects. Especially with old hardware like mine. Speaking of old hardware...

And then there's VirginControl. Having several virgins around simultaneously can be quite confusing for your Raunch Services.
Sorry. I could not resist. Feel free to delete this if it offends you in any way.  But do note that this joke is in no way Gender Specific.

