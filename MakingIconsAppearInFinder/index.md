---
layout: page
title: MakingIconsAppearInFinder
---





see also IconNotBeingIncluded

I used the following tricks to make sure that icons that I added to the Info.plist were displayed properly in the Finder.

For the application icon I had to log out and log back into my account before it would appear.

For the document icon I had to drag the application out of the 'build' folder onto the desktop; then I had to move the document file to a different folder. After I did those two things the document icons appeared properly.

- ChrisMeyer

*This may also (but not invariably) apply if you subsequently change the application icon file in a working project*

----

Ninety-nine percent of the time, 'touching' the .app bundle (by rebuilding it or by using the     touch command), then restarting the Finder refreshes the icons.

----

I had a strange bug in which under XCode 2.1 my icon would appear in the target's Properties tab and in the About this application under the Apple menu, but not in the Finder or the Dock. It was the generic application icon there.

The final resolution was to create a whole new icns file (with the same graphic as before), name it something different, delete the old icns file to the project, and add this new one. Then update the target's Icon File field under the Properties tab to point to this new icns file. This finally restored the icon to the Finder/Dock.

Other usual fixes, such as restarting, cleaning the target, deleting preferences, remaking the project, didn't work. Only remaking the icns file did.

----

I wanted to change the icons that would appear on the .tiff files that my application creates.  I did the following: I deleted the reference to the old .icns file from my project. I trashed the old.icns file and emptied the trash. I created a new .icns file using two tiffs: one for the icons and one for the masks using Icon Composer. I put the new .icns file in the project directory. I added a it to the project in the resources folder. When I click on it the project shows it in the editor correctly. I edited the project target properties so the UTI is public.tiff, the Icon File is the correct name of the .icns file including the .icns extension. I checked the package box to make sure the .icns file is included in the project bundle. I clicked on the .icns file in the project and hit command I to look at the file info for the .icns file. Under the Targets tab the Target Membership is shown as checked as a member of the application. I cleaned the targets. I moved the applications from the Build and Debug folders to the trash and emptied the trash. I restarted the computer. I logged out and back on. I restarted the finder. I rebuilt the project debug target. When I run my application and save my document as a tiff on the desk top it has the generic icon but if I get info for the file it still has the now extremely non-existenent old icon as its preview. If I move the file into a folder it has the old icon. It looks to me like the finder is the culprit here. It seems to be retaining the old icon somewhere.

Any more things I can do?

