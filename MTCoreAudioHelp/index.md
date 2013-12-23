---
layout: page
title: MTCoreAudioHelp
---

I am a newbie have been really trying to get MTCoreAudio to work and so far I have come up with this code. It runs(compiles with no errors or warnings) but the sound that comes out of the speakers is garbage noise. I understand that the problem is with not converting the buffer I create to a format that the input output callback will understand. However, I really don't know how to do it. Can someone please give me a small snippet of code to accomplish this task. Its been a real challenge coming up with this and any help is really appreciated.

The Interface File
    
#import <Cocoa/Cocoa.h>
#import <QTKit/QTKit.h>
#import <MTCoreAudio/MTCoreAudio.h>
#import <CoreAudio/CoreAudio.h>

@interface Controller : NSObject
{
    IBOutlet NSSlider *outputVolumeSlider;

    IBOutlet NSButton *playStopButton;

    MTCoreAudioDevice *outputDevice;
	
    NSArray *fileToOpen;
    NSData *myData;
}

- (IBAction) startPlaying: (id) sender;
- (IBAction) stopPlaying: (id) sender;

- (IBAction) changeOutputVolume: (id) sender;

- (void)byteCreator;

@end 



Implementation file
    
#import "Controller.h"
 
static unsigned char *g_soundBuffer;

static int g_lastIndex;

static int g_bufferSize;

@implementation Controller

- (OSStatus) writeCycleForDevice: (MTCoreAudioDevice *) theDevice 
		       timeStamp: (const AudioTimeStamp *) now 
		       inputData: (const AudioBufferList *) inputData 
		       inputTime: (const AudioTimeStamp *) inputTime 
		      outputData: (AudioBufferList *) outputData 
		      outputTime: (const AudioTimeStamp *) outputTime 
		      clientData: (void *) clientData
{ 

    if (g_lastIndex >= g_bufferSize) {

	[self performSelectorOnMainThread: @selector(stopPlaying:)
	      withObject: self
	      waitUntilDone: NO];

    } else {

	AudioBuffer *buffer;
	buffer = &outputData->mBuffers[0];
	
	memcpy (buffer->mData, g_soundBuffer + g_lastIndex, buffer->mDataByteSize);

	g_lastIndex += buffer->mDataByteSize;
    }
	
    return (noErr);
    
} 

- (void) setStuffBasedOnVolume
{
    MTCoreAudioVolumeInfo volumeInfo;

    volumeInfo = [outputDevice volumeInfoForChannel: 1 forDirection: kMTCoreAudioDevicePlaybackDirection];

    [outputVolumeSlider setFloatValue: volumeInfo.theVolume];

} 

- (void) awakeFromNib
{
    outputDevice = [MTCoreAudioDevice defaultOutputDevice];
    [outputDevice retain];

    [outputDevice setIOTarget: self
		  withSelector: @selector(writeCycleForDevice:timeStamp:inputData:inputTime:outputData:outputTime:clientData:)
		  withClientData: NULL];

    [self setStuffBasedOnVolume];
    
} 

- (void) dealloc
{
    [outputDevice release];

    [super dealloc];

}

- (IBAction) startPlaying: (id) sender
{
	int result;
	NSOpenPanel *oPanel = [NSOpenPanel openPanel];
	
	NSMutableArray *fileTypes = [QTMovie movieFileTypes:0];
	
	[oPanel setAllowsMultipleSelection:NO];
	[oPanel setCanChooseDirectories:NO];

	result = [oPanel runModalForTypes:fileTypes];
	
	if(result == NSOKButton)
	{
		fileToOpen = [oPanel filenames];
		[self byteCreator];
	}
	
	else
	{
		NSLog(@"Sound Not Loaded");
	}
    
    g_lastIndex = 0;

    [outputDevice deviceStart];

} 

- (void)byteCreator
{
	if(g_soundBuffer)
	{
		free(g_soundBuffer);
		g_soundBuffer = NULL;
	}
	g_lastIndex = 0;
	g_bufferSize = 0;
	NSString *theFileName = [fileToOpen objectAtIndex:0];
	myData = [NSData dataWithContentsOfFile:theFileName]; 
	g_soundBuffer = malloc([myData length]);
	[myData getBytes:g_soundBuffer];
	g_bufferSize = [myData length];
}

- (IBAction) stopPlaying: (id) sender
{
    [outputDevice deviceStop];

    [playStopButton setEnabled: NO];

} 

- (IBAction) changeOutputVolume: (id) sender
{
    [outputDevice setVolume: [outputVolumeSlider floatValue]
		 forChannel: 1
		 forDirection: kMTCoreAudioDevicePlaybackDirection];

    [outputDevice setVolume: [outputVolumeSlider floatValue]
		 forChannel: 2
		 forDirection: kMTCoreAudioDevicePlaybackDirection];
} 

@end 



Shaun.

----

Hey, you need to insert an audio converter in the callback function you have. Study the AudioConverter stuff in the OSX sample code (under coreaudio). Also (or? I don't use MTCoreAudio), when you create the default audio in awakeFromNib, you might want to set the output format there. In byteCreator I assume that you are opening a RAW file, otherwise you need to use some file loading API, either the built in AudioFile API, or maybe libsndfile (a personal favorite).
Just to let you know, I feel your pain, learning audio programming is very tough. It took me almost two years to really convert my app programming skills into audio programming skills, because there is scant info for beginners. Do a google search on realtime programming and one of the most useful hits is a DEC realtime programming manual from 1996, yeah, lots of support for newbies! One problem is that we literally have to unlearn a lot of what we thought was good programming practice in the app world.
Anyways, I am very, very close to releasing an open source audio engine with file/network streaming support, regions, editing, audio unit routing, the whole gamut, along with a comprehensive audio programming tutorial and copiously commented code. Stay tuned to cocoadev, and keep trying. JJJ

----

Thanks a lot. I'll look in to it. It's nice to know that I am not completely lost. I can't wait for the audio engine you will be releasing. Hopefully, it should free me from my worries. Till then, yes, I'll keep trying.

Shaun. 

----

Shaun,

I'm trying to do the same thing.  The sound rate is off in playback.  I am going to try to set the device sample rate with AudioDeviceSetProperty.  Let me know if you figured it out.  It would be much appreciated.

-- tim

