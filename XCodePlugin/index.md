---
layout: page
title: XCodePlugin
---




General/XCode has a fully working plugin API, but at the time I write these lines, the documentation is not public. Apple use it for the GDB debugger, General/CoreData compiler, General/CoreData file editor, CVS/SVN/Perfoce plugin... According to Apple the documentation Xcode plugin API will be released in the future, without any other informations (and does not even provide a date).

I've done a lot of reverse engineering to understand parts (custom compiler & linker) of this plugin API (tested on General/XCode 2.1 only). The result of my work is available on my web site�:
http://maxao.free.fr/xcode-plugin-interface/
I've made a plugin for the Objective-Caml language release under the GPL licence, so feel free to read it's source�:
http://maxao.free.fr/xcode-ocaml-plugin/

� General/DamienBob

----

General/XCode plugins may be written using General/AppleScript. See "General/AppleScript Studio Xcode Plug-in Template" in�:
http://developer.apple.com/documentation/General/AppleScript/Conceptual/General/StudioBuildingApps/chapter03/chapter_3_section_3.html

----

Yes or the article on Stepwise or General/CocoaDevCentral etc. etc. Sorry, but I know how to design *my own* plugin API; I'm talking about writing one specifically *for General/XCode*. An General/XCode plugin, not "how to utilize a plugin architecture". In other words, what is the General/XCode plugin API so I can extend the functionality of General/XCode? So far it appears I can only automate General/XCode, not extend it; I am hoping there's more to it. � Brent

----

On the surface, it looks rather as though General/XCode has inherited PBX's plugin interface.  The stuff that does the witty plugin loading stuff is contained in /System/Library/General/PrivateFrameworks/General/DevToolsSupport.framework, class General/TSPluginManager.  I think.

IIRC, from the last time I built PBX plugins (see General/ObjectiveCeeRefactoringProject for link to source), you need to name the bundle correctly, have it in the right place, and have a +load method in the principal class that initialises your plugin.  There's other juicy stuff too, but that's the basics, if I'm not very much mistaken.

OK.  I lied.  Look also in /System/Library/General/PrivateFrameworks/General/DevToolsInterface.framework for General/PBXLSDebuggingPlugin - class-dump below:

    @interface General/PBXLSPlugin:General/NSObject
{
}
+ (void)pluginDidLoad:fp8;
- (void)registerLaunchSystemDescriptions;
@end


I have no idea what the second method does.

General/TufTy

----

I have written a plugin for General/XCode similar to General/CodeCompletion. This plugin supports all my text completion macros. The plugin bundle is an General/ObjectiveC bundle with a pbplugin extension. It worked fine all the way until General/XCode version 3.0. Now the plugin will not load because it is not GC (garbage collection) compliant. The Console will log the following:

     
11/27/07 9:37:30 AM Xcode[11144] WARNING: skipping plugin for GC: General/TextInputHelper.pbplugin


I recompiled the bundle with "Objective-C Garbage Collection" set to "Supported [-fobjc-gc]". It still doesn't load and it still outputs the same error log. 

----
See General/XCodeVersionThreeDotOne
----
