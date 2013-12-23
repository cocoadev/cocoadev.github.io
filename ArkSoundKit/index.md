---
layout: page
title: ArkSoundKit
---


Hi, trollbot 3000 here,

I have been working on a portable sound engine for way too long, it just keeps growing and growing. Boy I have a problem setting scope! However, some of the Cocoa components have been ready for a while, so I just zipped them up and posted them on SourceForge for y'all.

The package includes the following classes:

    **ArkAudioUnit** - A thin wrapper around AUNode and AudioUnit

    **ArkAudioUnitManager** - A manager object to handle the AUGraph setup and create AudioUnit's by name

    **ArkAudioUnitEditor** - A simple but mostly complete class to open AudioUnit editor windows- both Carbon and Cocoa is supported

The code is released under the LGPL, but since I am the copyright holder I can relicense it at will. So if you want a more liberal license, just mail me jurksztowicz {a_t} gmail {d_o_t} com. Don't worry, I won't charge for a license unless you represent a fairly large company with deep pockets. I just want to get a good idea of who is using the code for what.
--JeremyJurksztowicz

http://sourceforge.net/projects/arksndkit/

----

Here is an example to illustrate what the code may be used for.

    
 //
 // A simple example of how you might create an audio engine using ArkSoundKit.
 //
 @interface Controller : NSObject
 {
 	ArkAudioUnit * output;
 	ArkAudioUnit * reverb;
 	ArkAudioUnit * mixer;
 }
 - (BOOL) createAudioEngine;
 @end
 
 - (BOOL) createAudioEngine
 {
 	//
 	// Setup AUGraph core.
 	//
 	[ArkAudioUnitManager installDefaultAUGraph];
 		
 	//
 	// Create output.
 	//
 	output = [[ArkAudioUnitManager defaultManager] createDefaultOutput];
 	if(!output)
 	{
 		NSLog(@"Error creating default output AudioUnit.");
 		return NO;
 	}
 		
 	//
 	// Create reverb.
 	//
 	reverb = [ArkAudioUnit audioUnitWithType:kAudioUnitType_Effect
 		subType:kAudioUnitSubType_MatrixReverb
 		manufacturer:kAudioUnitManufacturer_Apple];
 	if(!reverb)
 	{
 		NSLog(@"Error creating matrix reverb AudioUnit.");
 		return NO;
 	}
 	// Reverb is turned off by default.
 	else [reverb setBypassing:YES];
 	
 	//
 	// Create mixer.
 	//
 	NSString * mixerName = [[[ArkAudioUnitManager defaultManager] mixerNames] objectAtIndex:0];
 	mixer = [[ArkAudioUnitManager defaultManager] createMixerWithName:mixerName];
 	if(!mixer)
 	{
 		NSLog(@"Error creating mixer AudioUnit with name %@.", mixerName);
 		return NO;
 	}
 	
 	//
 	// Connect units.
 	//
 	[output connectInput:0 fromAudioUnit:reverb port:0];
 	[reverb connectInput:0 fromAudioUnit:mixer port:0];
 	
 	//
 	// Confirm, and return succesfully.
 	//
 	[output retain];
 	[reverb retain];
 	[mixer retain];
 	
 	return YES;
 }

