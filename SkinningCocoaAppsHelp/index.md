---
layout: page
title: SkinningCocoaAppsHelp
---

Has anyone found some good ways to skin apps in cocoa? I have nosed around a little and haven't found much via google and looking at the APIs, I am not seeing any kind of setting for a background image or anything. I guess you might be able to drop a Image or View onto the window, filling it with your image and then putting the controls on top of that, but it seems a clunky attempt. 

Anyone have any other ideas or solutions?

----
I spent a few days exploring drag-and-drop skinning a while back... http://homepage.mac.com/ryanstevens/Protege0.3.dmg

It's kind of buggy but since it was a developing-prototype it's not too terrible. I had meant to turn the control subclasses and skinning support stuff into a framework before 1.0 but that didn't happen. I can send you the source if you want though, maybe you could pound it into something useful.

----

*Anyone have any other ideas or solutions?*

I'd be tempted to try something based on webkit.

----

Why not go with a custom NSView that contains the controls? Draw the image onto the view. Make the skin a bundle so you can store custom button images, the background, etc, and store the position and size of each button and interface element (and the window size itself to match the background as needed) in a plist for TRUE skin-ability. On 'didLoadNib', read this in and position the controls, draw the background, etc. You can then distribute the skins as .dmg or .sit to preserve the bundle (and add readme's and other text files to your liking).

I should add at this point that I've never done this, but this is just how I'd try approaching it if I wanted to make a very flexible skin system for a cocoa app ...

Just my USD $0.02.

- JNozzi

----

The only problem with the bundle is that a lot of people wouldn't be able to make skins for the app as a lot have no clue about bundles and what not. However, this was the same thought I had originally, except instead of a bundle I was thinking a directory containing images and using the images based on their names. Something along the lines of ~/Library/Application Support/App Name/Skins/Skin Name containing images like mainwindow.png, buttonnames.png, etc. 

I think you're right though. The only way I can see to make it work would be to use an NSView or some such to hold the "window skin". 

----

It depends entirely on how far you want to go - creating a 'skinner' app that builds the bundles. I suppose you've got a valid point though - why make it a bundle? Just store everything in the folder and have the plist store the layout info.

- JNozzi

----
Whatever you do, make it more like InterfaceBuilder and less like programming a GUI by hand. A separate 'skinner app' would be 'ok' but having to follow some naming/filing conventions and hand editing .plists doesn't sound fun. Too much work on the skinners part = no skins.

Protege is a good example even though it was never fleshed out - it's its own 'skinner app'. Add an editing mode (if live skinning doesn't work out) and let the user drag and drop it all into place. I never got around to adding a generic load/save mechanism but it wouldn't have been too hard to stick it all into a bundle.

Source for Protege: http://homepage.mac.com/ryanstevens/temp/ProtegeSrc.zip

There's a lot I would do differently today and it was just a prototype but it might help.

