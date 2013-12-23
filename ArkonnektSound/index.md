---
layout: page
title: ArkonnektSound
---

Hello,
I just released the source code to my latest work in progress. It is a portable sound engine written in C++, with Cocoa AppKit extensions including a wave view and wave overview class (you know the kind you see in audio editors/multitrackers). A lot of the code is incomplete, but a few of the bits and pieces might be useful to Cocoa coders in general.

http://arksnd.sourceforge.net/

The classes which are more or less done, and ready to use are:

ArkAudioUnit		
- A cocoa class which provides a thin wrapper over the AudioUnit API.

ArkAudioUnitManager
- A class to manage creation of ArkAudioUnit's.

ArkAudioUnitEditor
- An NSWindowController subclass which opens an AudioUnit's Carbon OR Cocoa editor interface (See ArkAudioUnit_EditorAdditions.h).

ArkWaveView
- A view class which displays a sample buffer as a waveform. Uses a datasource/delegate model similar to NSTableView to separate the view from the model. Also provides arbitrary waveform zooming with peak caching for quick display.

Some classes which are not yet ready, but will be in a fairly short while:

ArkSound
- A replacement for NSSound with support for adding audio unit effects, detailed notification system, region selection, and lots more.

ArkAudioUnitChain
- An AudioUnit chain object, routs audio through an arbitrary chain of AudioUnit effects.

These are only the Cocoa classes which you might find useful, there are plenty of C++ classes which you may find useful.

In it's current form the project is very much unfinished, but if you download the source and compile it, you will find that a lot of the scaffolding for a full fledged audio editor is in place. I welcome any programmer willing to help, I hope we can make this into a really good audio editor with a true Mac OS X look and feel.

The C++ sound core is MIT licensed, the cocoa classes dealing with AudioUnit's is also MIT licensed, while the remaining Cocoa code, including the wave view classes, are LGPL licensed. I am the copyright holder to all the code, so if you want me to relicense some of the code, send me a good reason.

Jeremy Jurksztowicz [arketype] at [myrealbox] dot [com]

