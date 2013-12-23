---
layout: page
title: WhereDidItGo
---



[Topic]

Thought it would be nice to have a central location to talk about features that are moved or seem like they're missing. Feel free to replace questions with a description of how the feature changed --zootbobbalu

**Moved**

*Switch to Header/Source File menu item in PB (command + option + up arrow) has changed. There is a check box to adjust this behavior in the General pane titled "Counterparts always load in same editor". You can also adjust this behavior in separate editors with the button in the top right corner.
*Build settings and Plist. Select the target and choose Get Info from the Project menu.
*Compiler flags and build settings. This is now hierarchal. If you don't see a build setting you expect to see, check one of the other collections; it may have a default.
*The little check boxes that inform you which files are active for a particular Target are not visible by default. To get these check boxes back, simply right click (or Control + click) on the table header of the "Groups & Files" outline view, and select "Target Membership" from the popup menu. <- Smart Folders replaces this feature nicely :-)
*Runtime settings, such as launch arguments and environment variables: double-click on the executable under Executables in "Groups & Files"
*Build Styles - they're hidden in the Inspector (Command-I) for the project icon. For some reason, when I tried this first it didn't come up, but rather just opened the topmost source file... Note that the GUI for adding/removing build style settings has majorly improved, though, and you can even set everything up so the executable is stripped for deployment builds, which saves a lot of disk space. Before, you had to use pbxbuild for that.



**Behaviors Changed**

*The save panel no longer allows the return key to activate the save all button. To get around this simply change your preferences to automatically save before building (Preferences -> Building -> Unsaved Files -> Always Save)
*PB projects openned in General/XCode doesn't have the same toolbar as a native General/XCode project. Sometimes the Text View button is omitted. You can add this button by customizing the toolbar (View -> Customize Toolbar -> View Button)
*Files added to a project that exists outside the project folder are now broken if the project was created in PB. If someone knows how to import a PB project and preserve these links, please post here.


**Missing**

*A version that runs on Jaguar, as the WWDC seed did.
*A way to specify General/URLs that the application handles, which I think was supported by PB. Now you have to enter the appropriate keys into info.plist manually, and not change info.plist settings via the inspector.



----
Hmmm I have come accross something interesting that definately falls under this category... If you open up a classic project (.pbproj) and double-click on a target, you get the old (and preferable...) target editor. But, if you double-click on a target under a new project (.xcode) you get the .pch file. I would prefer much the old target editor...

-- Xyph3r:.�

*I think it has to do with the Jambase used in Xcode. Cocoa-Java projects still use the old style target editor. To replicate this behavior, try creating one of the targets marked Legacy in your Xcode project.*

**Do cmd-I on the target instead.  That's Where It Went�**
