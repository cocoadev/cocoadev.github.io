---
layout: page
title: MokitFrameworkInstallProblems
---



I'm having problems installing the Mokit Framework, here is what I'm doing, please let me know where I am going wrong...

Download copy of Mokit, make copy on desktop called MOKit_embed. Move this folder to ~/Library/Frameworks
Double click ~/Library/Frameworks/MOKit_embed/MOKit.pbproj and open in Xcode. Xcode gives me a warning "The project "MOKit.pbproj" was saved with an older version of Xcode. To open it, you must first make a copy of it, which will have the '.xcodeproj' extension. Are you sure you want to open an upgraded copy?"
I click 'upgrade' and save as ~/Library/Frameworks/MOKit_embed/MOKit.xcodeproj

In Xcode.. 
I leave 'Active Build Configuration' on 'Development'
I change 'Active Target' from 'Mokit All' to 'Mokit'
I press 'Build' and get warnings...
Compiling MoKit/MOViewController.m
warning: conflicting types for '-(NSView *)view'
warning: previous declaration of '-(id)view'
warning: method possibly missing a [super dealloc] call

Ignoring the warnings I start a new cocoa application project in Xcode saved as 'test2' in  ~/Documents/COCOA/

In Xcode...
From the main menu, I choose Project/Add To Project  where I then choose ~/Library/Frameworks/MOKit_embed/build/Development/Mokit.framework/Mokit then press the 'Add' button.

To test if all OK I press 'Build and Go' and get the following in the log...
"[Session started at 2007-07-30 12:45:03 +0100.]
dyld: Library not loaded: /Library/Frameworks/MOKit.framework/Versions/A/MOKit
  Referenced from: /Users/benmilford/Documents/COCOA/test2/build/Debug/test2.app/Contents/MacOS/test2
  Reason: image not found

test2 has exited due to signal 5 (SIGTRAP)."

I'm presuming the above log is incorrect, where am I going wrong?

----
The log is probably correct. You seem to have linked your final executable expecting to find the MoKit framework (dynamic library) in     /Library/Frameworks and there is not a copy of it there.

You would find it a bit easier to keep straight if you located the     MOKit_embed source tree in     /Users/benmilford/Documents/COCOA, and copied the built product (    Mokit.framework) to     ~/Library/Frameworks , but this is not at all necessary (and may not be desirable, actually) if you are going to embed the library - like any other source or resource, just place a copy in the source tree of your project, perhaps     /Users/benmilford/Documents/COCOA/test2 , and drag it into the     Frameworks folder of the Project tree/pane in XCode.

See MoreOnEmbeddingFrameworks for a worked example.

