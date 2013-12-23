---
layout: page
title: CreatingFinderPlugins
---

Hey everyone,

I'm looking into writing some sort of Finder plugin to extend its functionality, so here is a couple of questions:

1) One of the only 'good' plugin examples I found was here:
http://developer.apple.com/samplecode/SampleCMPlugIn/listing2.html
As far as I understood, it adds a global context menu item or something in that order. Is the the simplest form of the Finder plugin that I can have? It seems a bit overcomplicated

2) if I'm writing a Finder plugin, does it have to be written in Carbon? I always thought that Cocoa is the main toolkit for everything visual in OS X
3) Is it possible to create a plugin that does something more powerful, say, add another view to Finder?

4) I looked at CFPlugin page, but is there a page that specifically discusses Finder plugin API and where to put the plugin bundles to enable them? And also stuff like, what can i call from my plugin once I registered it with Finder?


Thanks a bunch.

--DimitriT
----
> *I always thought that Cocoa is the main toolkit for everything visual in OS X*

No, not really. Cocoa relies on a number of C API's. It even uses Carbon in a few areas. The Finder itself is a Carbon application.
----

Everything visual in OSX eventually relies on Quartz.  Quartz is a C API.  Cocoa's AppKit relies on ApplicationServices which in turns relies on Quartz.  Context Menus aren't just finder plugins, but in ANY context menu in ANY application.  Other than this there is no real way to add things to the finder... especially not Cocoa NSViews, because as pointed out above, the Finder is Carbon.  CFPlugin stands for "CoreFoundation" Plugin.  CoreFoundation is a subset of Carbon APIs (in C) that work in Carbon apps in OS9, and OSX, and are the underpinning of Cocoa's FoundationFramework.  CFPlugins are a standard way to build plugin-based architectures for Carbon-y things, and there is support in the API for things like dynamically loading/linking the code. 

What it sounds like you want to do is add functionality to the Finder including a view.  Have you considered building an external application and making it have no dock icon/menu by playing with the plist.  This is how the archiving functionality works in the Finder.  A seperate application (/System/Library/CoreServices/BOMArchiveHelper) adds a CMI like your example, registers itself to open "zip" archives and has info.plist entries telling the dock not to give it an icon, and the window manager not to give it an application menu. (I forget exactly which key you set for this... try searching macosxhints.com)

----
Thanks a lot for info, that explained a few things. Although, what about these guys? http://scplugin.tigris.org/
I've looked through the code, but was having hard time understanding how did they plug into finded to change icons and stuff like that.

----
Ouch,

They are hooking a system level function (GetIconRefFromFileInfo). Look at http://svn.red-bean.com/scplugin/trunk/SCPlugin/UserInterface/IconOverlays/SCFinderIconOverlay.m; and http://svn.red-bean.com/scplugin/trunk/SCPlugin/UserInterface/IconOverlays/mach_override.c

I can't say I would recommend doing this.

