---
layout: page
title: SoundVolume
---

In something related to KeyCodesAndCharCodes, I've been trying to work out how to set the system volume from within an objective-c app.  While it seems easy enough to tie into the applescript Sripting Additions volume thing, that has the disadvantage that it's only stored in the OS9 System Folder's scripting application folder.  So, no classic install - no sound volume changes.  Can anyone recommend the best way of changing the system volume from a cocoa app?  Ideally, how would one do so not to set levels but relative to current levels? http://goo.gl/OeSCu

The best way of achieving this would be to use the same calls as the keyboard volume buttons do, so that the user also gets the lovely visual feedback as to the volume level.  How does one call that event, or is it only possible to do this via faking the key presses themselves?  That might be possible using CGPostKeyboardEvent (see KeyCodesAndCharCodes)...

--Fred

----

No progress on hooking into the system sound calls for pretty on-screen displays, but as usual, the header files provide more information than most of the Apple developer website; to get the system volume, use the Carbon call GetDefaultOutputVolume(long * level), and to set it use SetDefaultOutputVolume(long level).  It's by no means relative, strictly absolute, but with those two it's possible to generate a relative level.

More info as I keep glomming the headers :) --Fred

----

small correction from above code,
appearantly some devices -only- support the "master channel" (channel 0), 
others only support stereo channels.

so the code below checks both


 #import <CoreAudio/CoreAudio.h>
 
 // getting system volume
 
 - (float)getVolume {
 	float			b_vol;
 	OSStatus		err;
 	AudioDeviceID		device;
 	UInt32			size;
 	UInt32			channels[2];
 	float			volume[2];
 	
 	// get device
 	size = sizeof device;
 	err = AudioHardwareGetProperty(kAudioHardwarePropertyDefaultOutputDevice, &size, &device);
 	if(err!=noErr) {
 		NSLog(@"audio-volume error get device");
 		return 0.0;
 	}
 	
 	// try set master volume (channel 0)
 	size = sizeof b_vol;
 	err = AudioDeviceGetProperty(device, 0, 0, kAudioDevicePropertyVolumeScalar, &size, &b_vol);	//kAudioDevicePropertyVolumeScalarToDecibels
 	if(noErr==err) return b_vol;
 	
 	// otherwise, try seperate channels
 	// get channel numbers
 	size = sizeof(channels);
 	err = AudioDeviceGetProperty(device, 0, 0,kAudioDevicePropertyPreferredChannelsForStereo, &size,&channels);
 	if(err!=noErr) NSLog(@"error getting channel-numbers");
 	
 	size = sizeof(float);
 	err = AudioDeviceGetProperty(device, channels[0], 0, kAudioDevicePropertyVolumeScalar, &size, &volume[0]);
 	if(noErr!=err) NSLog(@"error getting volume of channel %d",channels[0]);
 	err = AudioDeviceGetProperty(device, channels[1], 0, kAudioDevicePropertyVolumeScalar, &size, &volume[1]);
 	if(noErr!=err) NSLog(@"error getting volume of channel %d",channels[1]);
 	
 	b_vol = (volume[0]+volume[1])/2.00;
 	
 	return  b_vol;
 }
 
 
 // setting system volume
 - (void)setVolume:(float)involume {
 	OSStatus		err;
 	AudioDeviceID		device;
 	UInt32			size;
 	Boolean			canset	= false;
 	UInt32			channels[2];
 	//float			volume[2];
 		
 	// get default device
 	size = sizeof device;
 	err = AudioHardwareGetProperty(kAudioHardwarePropertyDefaultOutputDevice, &size, &device);
 	if(err!=noErr) {
 		NSLog(@"audio-volume error get device");
 		return;
 	}
 	
 	
 	// try set master-channel (0) volume
 	size = sizeof canset;
 	err = AudioDeviceGetPropertyInfo(device, 0, false, kAudioDevicePropertyVolumeScalar, &size, &canset);
 	if(err==noErr && canset==true) {
 		size = sizeof involume;
 		err = AudioDeviceSetProperty(device, NULL, 0, false, kAudioDevicePropertyVolumeScalar, size, &involume);
 		return;
 	}
 
 	// else, try seperate channes
 	// get channels
 	size = sizeof(channels);
 	err = AudioDeviceGetProperty(device, 0, false, kAudioDevicePropertyPreferredChannelsForStereo, &size,&channels);
 	if(err!=noErr) {
 		NSLog(@"error getting channel-numbers");
 		return;
 	}
 	
 	// set volume
 	size = sizeof(float);
 	err = AudioDeviceSetProperty(device, 0, channels[0], false, kAudioDevicePropertyVolumeScalar, size, &involume);
 	if(noErr!=err) NSLog(@"error setting volume of channel %d",channels[0]);
 	err = AudioDeviceSetProperty(device, 0, channels[1], false, kAudioDevicePropertyVolumeScalar, size, &involume);
 	if(noErr!=err) NSLog(@"error setting volume of channel %d",channels[1]);
 	
 }


.arri


----


This code uses the new Leopard Audio Hardware Services API (ie won't work on Tiger) and is much cleaner, as kAudioHardwareServiceDeviceProperty_VirtualMasterVolume will either set the master volume if the device has one, or adjust each channel relative to one another. Link against AudioToolbox]].Framework.

 #import <AudioToolbox]]/AudioServices]].h>
 
 // getting system volume
 
 +(float)volume 
 {
 	Float32			outputVolume;
 	
 	UInt32 propertySize = 0;
 	OSStatus status = noErr;
 	AudioObjectPropertyAddress]] propertyAOPA;
 	propertyAOPA.mElement = kAudioObjectPropertyElementMaster;
 	propertyAOPA.mSelector = kAudioHardwareServiceDeviceProperty_VirtualMasterVolume;
 	propertyAOPA.mScope = kAudioDevicePropertyScopeOutput;
 	
 	AudioDeviceID outputDeviceID = [KNVolumeControl defaultOutputDeviceID];
 		
 	if (outputDeviceID == kAudioObjectUnknown)
 	{
 		NSLog(@"Unknown device");
 		return 0.0;
 	}
 	
 	if (!AudioHardwareServiceHasProperty(outputDeviceID, &propertyAOPA))
 	{
 		NSLog(@"No volume returned for device 0x%0x", outputDeviceID);
 		return 0.0;
 	}
 	
 	propertySize = sizeof(Float32);
 	
 	status = AudioHardwareServiceGetPropertyData(outputDeviceID, &propertyAOPA, 0, NULL, &propertySize, &outputVolume);
 	
 	if (status)
 	{
 		NSLog(@"No volume returned for device 0x%0x", outputDeviceID);
 		return 0.0;
 	}
 	
 	if (outputVolume < 0.0 || outputVolume > 1.0) return 0.0;
 	
 	return outputVolume;
 }
 
 // setting system volume - mutes if under threshhold 
 +(void)setVolume:(Float32)newVolume
 {
 if (newVolume < 0.0 || newVolume > 1.0)
 	{
 		NSLog("Requested volume out of range (%.2f)", newVolume);
 		return;
 		
 	}
 
 	// get output device device
 	UInt32 propertySize = 0;
 	OSStatus status = noErr;
 	AudioObjectPropertyAddress]] propertyAOPA;
 	propertyAOPA.mElement = kAudioObjectPropertyElementMaster;
 	propertyAOPA.mScope = kAudioDevicePropertyScopeOutput;
 	
 	if (newVolume < 0.001)
 	{
 		NSLog("Requested mute");
 		propertyAOPA.mSelector = kAudioDevicePropertyMute;
 
 	}
 	else
 	{
 		NSLog("Requested volume %.2f", newVolume);
 		propertyAOPA.mSelector = kAudioHardwareServiceDeviceProperty_VirtualMasterVolume;
 	}
 	
 	AudioDeviceID outputDeviceID = [KNVolumeControl defaultOutputDeviceID];
 	
 	if (outputDeviceID == kAudioObjectUnknown)
 	{
 		NSLog(@"Unknown device");
 		return;
 	}
 	
 	if (!AudioHardwareServiceHasProperty(outputDeviceID, &propertyAOPA))
 	{
 		NSLog(@"Device 0x%0x does not support volume control", outputDeviceID);
 		return;
 	}
 	
 	Boolean canSetVolume = NO;
 	
 	status = AudioHardwareServiceIsPropertySettable(outputDeviceID, &propertyAOPA, &canSetVolume);
 	
 	if (status || canSetVolume == NO)
 	{
 		NSLog(@"Device 0x%0x does not support volume control", outputDeviceID);
 		return;
 	}
 	
 	if (propertyAOPA.mSelector == kAudioDevicePropertyMute)
 	{
 		propertySize = sizeof(UInt32);
 		UInt32 mute = 1;
 		status = AudioHardwareServiceSetPropertyData(outputDeviceID, &propertyAOPA, 0, NULL, propertySize, &mute);		
 	}
 	else
 	{
 		propertySize = sizeof(Float32);
 		
 		status = AudioHardwareServiceSetPropertyData(outputDeviceID, &propertyAOPA, 0, NULL, propertySize, &newVolume);	
 		
 		if (status)
 		{
 			NSLog(@"Unable to set volume for device 0x%0x", outputDeviceID);
 		}
 		
 		// make sure we're not muted
 		propertyAOPA.mSelector = kAudioDevicePropertyMute;
 		propertySize = sizeof(UInt32);
 		UInt32 mute = 0;
 		
 		if (!AudioHardwareServiceHasProperty(outputDeviceID, &propertyAOPA))
 		{
 			NSLog(@"Device 0x%0x does not support muting", outputDeviceID);
 			return;
 		}
 		
 		Boolean canSetMute = NO;
 		
 		status = AudioHardwareServiceIsPropertySettable(outputDeviceID, &propertyAOPA, &canSetMute);
 		
 		if (status || !canSetMute)
 		{
 			NSLog(@"Device 0x%0x does not support muting", outputDeviceID);
 			return;
 		}
 		
 		status = AudioHardwareServiceSetPropertyData(outputDeviceID, &propertyAOPA, 0, NULL, propertySize, &mute);
 	}
 		
 	if (status)
 	{
 		NSLog(@"Unable to set volume for device 0x%0x", outputDeviceID);
 	}
 
 }
 
 +(AudioDeviceID)defaultOutputDeviceID
 {
 	AudioDeviceID	outputDeviceID = kAudioObjectUnknown;
 	
 	// get output device device
 	UInt32 propertySize = 0;
 	OSStatus status = noErr;
 	AudioObjectPropertyAddress]] propertyAOPA;
 	propertyAOPA.mScope = kAudioObjectPropertyScopeGlobal;
 	propertyAOPA.mElement = kAudioObjectPropertyElementMaster;
 	propertyAOPA.mSelector = kAudioHardwarePropertyDefaultOutputDevice;
 	
 	if (!AudioHardwareServiceHasProperty(kAudioObjectSystemObject, &propertyAOPA))
 	{
 		NSLog(@"Cannot find default output device!");
 		return outputDeviceID;
 	}
 	
 	propertySize = sizeof(AudioDeviceID);
 	
 	status = AudioHardwareServiceGetPropertyData(kAudioObjectSystemObject, &propertyAOPA, 0, NULL, &propertySize, &outputDeviceID);
 	
 	if(status) 
 	{
 		NSLog(@"Cannot find default output device!");
 	}
 	return outputDeviceID;
 }


-Ryan 

(Updated to mute the device at very low volumes, after discovering that setting 0.0 often doesnt silence the device. Also removed obselete SoundManager code and non-canonical pre-Leopard code).


----

A command line tool to set the system sound value (Mac OS X 10.4).


 /*
 
 setvol - set system sound volume  (Mac OS X 10.4)
 
 Usage:
 
 setvol 0
 setvol 0.5
 setvol 1
 
 code based on:
 http://www.cocoadev.com/index.pl?SoundVolume  (CoreAudio snippet by .arri)
 
 compile with:
 gcc -Wall -O3 -x objective-c -framework Foundation -framework CoreAudio -o setvol setvol.c
 
 */
 
 #import <Foundation/Foundation.h>
 #import <CoreAudio/CoreAudio.h>
 
 int main (int argc, char *argv[]) {
 
    NSAutoreleasePool *pool = [NSAutoreleasePool new];
 
    OSStatus           err;
    AudioDeviceID      device;
    UInt32             size;
    Boolean            canset = false;
    UInt32             channels[2];
    //float            volume[2];
    float              involume;
 
    if (argc != 2) return 1;
 
    NSString *input = [NSString stringWithUTF8String: argv[1]];
    involume = [input floatValue];
 
    if (involume < 0.0 || involume > 1.0) return 1;
                 
    // get default device
    size = sizeof device;
    err = AudioHardwareGetProperty(kAudioHardwarePropertyDefaultOutputDevice, &size, &device);
    if(err!=noErr) {
       NSLog(@"audio-volume error get device");
       return 1;
    }
 
    //NSLog(@"Device found: 0x%0x", device);
        
    // try set master-channel (0) volume
    size = sizeof canset;
    err = AudioDeviceGetPropertyInfo(device, 0, false, kAudioDevicePropertyVolumeScalar, &size, &canset);
    if(err==noErr && canset==true) {
       size = sizeof involume;
       err = AudioDeviceSetProperty(device, NULL, 0, false, kAudioDevicePropertyVolumeScalar, size, &involume);
       return 0;
    }
 
    // else, try seperate channes
    // get channels
    size = sizeof(channels);
    err = AudioDeviceGetProperty(device, 0, false, kAudioDevicePropertyPreferredChannelsForStereo, &size,&channels);
    if(err!=noErr) {
       NSLog(@"error getting channel-numbers");
       return 1;
    }
         
    // set volume
    size = sizeof(float);
    err = AudioDeviceSetProperty(device, 0, channels[0], false, kAudioDevicePropertyVolumeScalar, size, &involume);
    if(noErr!=err) NSLog(@"error setting volume of channel %d",channels[0]);
    err = AudioDeviceSetProperty(device, 0, channels[1], false, kAudioDevicePropertyVolumeScalar, size, &involume);
    if(noErr!=err) NSLog(@"error setting volume of channel %d",channels[1]);
 
    [pool release];
 
    return 0;
 
 }



----

A command line tool to set the system sound value (Mac OS X 10.6).





 /*
 
 setvol - set system sound volume (Mac OS X 10.6)
 
 Usage:
 
 ./setvol 0
 ./setvol 0.5
 ./setvol 1
 
 code based on:
 http://www.cocoadev.com/index.pl?SoundVolume  (AudioToolbox snippet by Ryan)
 
 compile with:
 gcc -Wall -O3 -x objective-c -framework AudioToolbox -framework Foundation -framework CoreAudio -o setvol setvol.c
 
 */
 
 #import <Foundation/Foundation.h>
 #import <CoreAudio/CoreAudio.h>
 #import <AudioToolbox/AudioServices.h>
 
 
 @interface KNVolumeControl : NSObject 
 {
 float volume;
 AudioDeviceID defaultOutputDeviceID;
 }
 
 +(float)volume;
 // setting system volume - mutes if under threshhold 
 +(void)setVolume:(Float32)newVolume;
 +(AudioDeviceID)defaultOutputDeviceID;
 
 @end
 
 
 @implementation KNVolumeControl
 
 +(float)volume 
 {
 	Float32			outputVolume;
 	
 	UInt32 propertySize = 0;
 	OSStatus status = noErr;
 	AudioObjectPropertyAddress propertyAOPA;
 	propertyAOPA.mElement = kAudioObjectPropertyElementMaster;
 	propertyAOPA.mSelector = kAudioHardwareServiceDeviceProperty_VirtualMasterVolume;
 	propertyAOPA.mScope = kAudioDevicePropertyScopeOutput;
 	
 	AudioDeviceID outputDeviceID = [KNVolumeControl defaultOutputDeviceID];
 		
 	if (outputDeviceID == kAudioObjectUnknown)
 	{
 		NSLog(@"Unknown device");
 		return 0.0;
 	}
 	
 	if (!AudioHardwareServiceHasProperty(outputDeviceID, &propertyAOPA))
 	{
 		NSLog(@"No volume returned for device 0x%0x", outputDeviceID);
 		return 0.0;
 	}
 	
 	propertySize = sizeof(Float32);
 	
 	status = AudioHardwareServiceGetPropertyData(outputDeviceID, &propertyAOPA, 0, NULL, &propertySize, &outputVolume);
 	
 	if (status)
 	{
 		NSLog(@"No volume returned for device 0x%0x", outputDeviceID);
 		return 0.0;
 	}
 	
 	if (outputVolume < 0.0 || outputVolume > 1.0) return 0.0;
 	
 	return outputVolume;
 }
 
 
 
 // setting system volume - mutes if under threshhold 
 +(void)setVolume:(Float32)newVolume
 {
 if (newVolume < 0.0 || newVolume > 1.0)
 	{
 		NSLog(@"Requested volume out of range (%.2f)", newVolume);
 		return;
 		
 	}
 
 	// get output device device
 	UInt32 propertySize = 0;
 	OSStatus status = noErr;
 	AudioObjectPropertyAddress propertyAOPA;
 	propertyAOPA.mElement = kAudioObjectPropertyElementMaster;
 	propertyAOPA.mScope = kAudioDevicePropertyScopeOutput;
 	
 	if (newVolume < 0.001)
 	{
 		NSLog(@"Requested mute");
 		propertyAOPA.mSelector = kAudioDevicePropertyMute;
 
 	}
 	else
 	{
 		NSLog(@"Requested volume %.2f", newVolume);
 		propertyAOPA.mSelector = kAudioHardwareServiceDeviceProperty_VirtualMasterVolume;
 	}
 	
 	AudioDeviceID outputDeviceID = [KNVolumeControl defaultOutputDeviceID];
 	
 	if (outputDeviceID == kAudioObjectUnknown)
 	{
 		NSLog(@"Unknown device");
 		return;
 	}
 	
 	if (!AudioHardwareServiceHasProperty(outputDeviceID, &propertyAOPA))
 	{
 		NSLog(@"Device 0x%0x does not support volume control", outputDeviceID);
 		return;
 	}
 	
 	Boolean canSetVolume = NO;
 	
 	status = AudioHardwareServiceIsPropertySettable(outputDeviceID, &propertyAOPA, &canSetVolume);
 	
 	if (status || canSetVolume == NO)
 	{
 		NSLog(@"Device 0x%0x does not support volume control", outputDeviceID);
 		return;
 	}
 	
 	if (propertyAOPA.mSelector == kAudioDevicePropertyMute)
 	{
 		propertySize = sizeof(UInt32);
 		UInt32 mute = 1;
 		status = AudioHardwareServiceSetPropertyData(outputDeviceID, &propertyAOPA, 0, NULL, propertySize, &mute);		
 	}
 	else
 	{
 		propertySize = sizeof(Float32);
 		
 		status = AudioHardwareServiceSetPropertyData(outputDeviceID, &propertyAOPA, 0, NULL, propertySize, &newVolume);	
 		
 		if (status)
 		{
 			NSLog(@"Unable to set volume for device 0x%0x", outputDeviceID);
 		}
 		
 		// make sure we're not muted
 		propertyAOPA.mSelector = kAudioDevicePropertyMute;
 		propertySize = sizeof(UInt32);
 		UInt32 mute = 0;
 		
 		if (!AudioHardwareServiceHasProperty(outputDeviceID, &propertyAOPA))
 		{
 			NSLog(@"Device 0x%0x does not support muting", outputDeviceID);
 			return;
 		}
 		
 		Boolean canSetMute = NO;
 		
 		status = AudioHardwareServiceIsPropertySettable(outputDeviceID, &propertyAOPA, &canSetMute);
 		
 		if (status || !canSetMute)
 		{
 			NSLog(@"Device 0x%0x does not support muting", outputDeviceID);
 			return;
 		}
 		
 		status = AudioHardwareServiceSetPropertyData(outputDeviceID, &propertyAOPA, 0, NULL, propertySize, &mute);
 	}
 		
 	if (status)
 	{
 		NSLog(@"Unable to set volume for device 0x%0x", outputDeviceID);
 	}
 
 }
 
 
 +(AudioDeviceID)defaultOutputDeviceID
 {
 	AudioDeviceID	outputDeviceID = kAudioObjectUnknown;
 	
 	// get output device device
 	UInt32 propertySize = 0;
 	OSStatus status = noErr;
 	AudioObjectPropertyAddress propertyAOPA;
 	propertyAOPA.mScope = kAudioObjectPropertyScopeGlobal;
 	propertyAOPA.mElement = kAudioObjectPropertyElementMaster;
 	propertyAOPA.mSelector = kAudioHardwarePropertyDefaultOutputDevice;
 	
 	if (!AudioHardwareServiceHasProperty(kAudioObjectSystemObject, &propertyAOPA))
 	{
 		NSLog(@"Cannot find default output device!");
 		return outputDeviceID;
 	}
 	
 	propertySize = sizeof(AudioDeviceID);
 	
 	status = AudioHardwareServiceGetPropertyData(kAudioObjectSystemObject, &propertyAOPA, 0, NULL, &propertySize, &outputDeviceID);
 	
 	if(status) 
 	{
 		NSLog(@"Cannot find default output device!");
 	}
 	return outputDeviceID;
 }
 
 
 @end
 
 
 
 int main (int argc, char *argv[]) {
 
    NSAutoreleasePool * pool = NSAutoreleasePool alloc] init];
 
    Float32 involume;
 
    if (argc != 2) return 1;
 
    NSString *input = [NSString stringWithUTF8String: argv[1;
    involume = [input floatValue];
 
    if (involume < 0.0 || involume > 1.0) return 1;
 
    [KNVolumeControl setVolume: involume];   
 
    [pool release];
 
    return 0;
 
 }

