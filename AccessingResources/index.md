---
layout: page
title: AccessingResources
---

Bundles in Mac OS X provide a way to store resources an application may need like sounds, images, and configuration files without exposing the user to a useless mess of files.

To have a file be automatically included in your application's bundle at compile time, place it in the "Resources" group.  You can then access this bundle using the NSBundle class.  The general strategy for accessing a bundle resource is to send an instance of NSBundle a message that returns a path to the specified resource, and then we access the resource using the returned path.

Say we have a property list document in the bundle called aPropertyList.plist.  We can then access it in the following way:

    

NSBundle *bundle = [NSBundle mainBundle];
NSString *path = [bundle pathForResource:@"aPropertyList" ofType:@"plist"];

// do something with the path to load the file appropriately


Note that we don't write the file type out as @".plist", but @"plist"--no period.

----

I just had quite a headache involving NSBundles (in the form of an application plugin) and I thought I'd share my experience so that others can more quickly remedy a similar dilemma.  The situation is this:

I wanted to use a special picture in a plugin so I made one in photoshop and added it into my project.  Then I tried to use [NSImage imageNamed:@"filename"].  Nothing showed up.  After pulling my hair for a while, I decided to try something strange.  I added the picture file to the main application (the one that loaded the plugin) and voil� it worked fine.  The problem was that the imageNamed: method was searching ONLY the main bundle, and not the plugin's bundle.

The correct code ended up being something like this:
    
NSBundle *bundle = [NSBundle bundleForClass:[self class]];
NSString *path = [bundle pathForResource:@"filename" ofType:@"tif"];
NSImage* picture = [[NSImage alloc] initWithContentsOfFile:path];


This should be easy to follow - I find the bundle that contains my plugin's class, search its contents for the file I want, then load that file as a new picture.  I hope that people find this useful.
-ColinTA

