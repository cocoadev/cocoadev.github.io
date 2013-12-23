---
layout: page
title: QuickTimeAudioExtractionExample
---



Hello,

I'm trying to use Apple's Audio Extraction API (http://developer.apple.com/quicktime/audioextraction.html) to write a method that will load a WAV file (or any media file, I suppose) and put the audio contents into a buffer of floats.

It's very hacky and full of potential bugs (ie, I've hardcoded the size of the buffers, for a start!) but I'll fix those later. For the time being I want to know why the actual call to MovieAudioExtractionFillBuffer is failing with code -2209 (badCallOrderErr), since everything right up to that call runs correctly (or so it appears to in the debugger).

Can anyone see what I'm doing wrong (other than the obvious hack type stuff)?

Any suggestions most appreciated!


- Ade 13/02/06

----

I thought I'd follow up on my original post. I've updated the code below and this mostly works for pretty much any media file containing audio. It takes an NSString with a URL (such as @"file:///test.mov"), imports it and stores it as interleaves floats in a buffer called samples* (which you need to provide). sampleCount is a count of the number of samples, too.

Caveat: Bugs lie herein, code could be much more stable but seems to work. Non-stereo files seem to fail, perhaps I have something wrong in the way I set up the asbd. Any comments/advice warmly welcomed.


- Ade 06/03/06

    

float* samples;
UInt32 sampleCount;

// import from a URL, such as file:///test.mov
- (void) importAudioFromFile:(NSString*)path {
	
	OSErr						error; 
	Handle						dataRef; 
	OSType						dataRefType; 
	CFURLRef					fileLocation;
	Movie						soundToPlay;
	MovieAudioExtractionRef		extractionSessionRef = nil;
	AudioChannelLayout*			layout			 = nil;
	UInt32						size				= 0;
	AudioStreamBasicDescription	asbd;

	NSLog(@"EnterMovies...");		
	EnterMovies();

	NSLog(@"Setting fileLocation...");
	fileLocation = CFURLCreateWithString(NULL, path, NULL);

	NSLog(@"Creating new data reference...");
	error = QTNewDataReferenceFromCFURL(fileLocation, 0, &dataRef, &dataRefType);
	NSLog(@"   %d",error);
	
	NSLog(@"Creating new movie...");
	short fileID = movieInDataForkResID; 
	short flags = 0; 
	error = NewMovieFromDataRef(&soundToPlay, flags, &fileID, dataRef, dataRefType);
	NSLog(@"   %d",error);

	NSLog(@"Setting movie active...");
	SetMovieActive(soundToPlay, TRUE);

	NSLog(@"Beginning extraction session...");
	error = MovieAudioExtractionBegin(soundToPlay, 0, &extractionSessionRef); 
	NSLog(@"   %d",error);

	NSLog(@"Getting property info...");
	error = MovieAudioExtractionGetPropertyInfo(extractionSessionRef,
			kQTPropertyClass_MovieAudioExtraction_Audio,
			kQTMovieAudioExtractionAudioPropertyID_AudioChannelLayout,
			NULL, &size, NULL);
	NSLog(@"   %d",error);
		
	if (error == noErr) {
		// Allocate memory for the channel layout
		layout = calloc(1, size);
		if (layout == nil) {
			error = memFullErr;
			NSLog(@"Oops, out of memory");
		}
		// Get the layout for the current extraction configuration.
		// This will have already been expanded into channel descriptions.
		NSLog(@"Getting property...");
		error = MovieAudioExtractionGetProperty(extractionSessionRef,
				kQTPropertyClass_MovieAudioExtraction_Audio,
				kQTMovieAudioExtractionAudioPropertyID_AudioChannelLayout,
				size, layout, nil);   
		NSLog(@"   %d",error);
	}
	
	NSLog(@"Getting audio stream basic description (absd)...");
	error = MovieAudioExtractionGetProperty(extractionSessionRef,
			kQTPropertyClass_MovieAudioExtraction_Audio,
			kQTMovieAudioExtractionAudioPropertyID_AudioStreamBasicDescription,
			sizeof (asbd), &asbd, nil);
	NSLog(@"   %d",error);
	
	NSLog(@"   format flags   = %d",asbd.mFormatFlags);
	NSLog(@"   sample rate    = %f",asbd.mSampleRate);
	NSLog(@"   b/packet       = %d",asbd.mBytesPerPacket);
	NSLog(@"   f/packet       = %d",asbd.mFramesPerPacket);
	NSLog(@"   b/frame        = %d",asbd.mBytesPerFrame);
	NSLog(@"   channels/frame = %d",asbd.mChannelsPerFrame);
	NSLog(@"   b/channel      = %d",asbd.mBitsPerChannel);
	
	if (asbd.mChannelsPerFrame != 2) {
		NSLog(@"Cannot import non-stereo audio!");
	}
	
	asbd.mFormatFlags = kAudioFormatFlagIsFloat |
						kAudioFormatFlagIsPacked |
						kAudioFormatFlagsNativeEndian; // NOT kAudioFormatFlagIsNonInterleaved!
	asbd.mChannelsPerFrame = 2;
	asbd.mBitsPerChannel = sizeof(float) * 8;
	asbd.mBytesPerFrame = sizeof(float) * asbd.mChannelsPerFrame;
	asbd.mBytesPerPacket = asbd.mBytesPerFrame;

	NSLog(@"Setting new asbd...");
	error = MovieAudioExtractionSetProperty(extractionSessionRef,
			kQTPropertyClass_MovieAudioExtraction_Audio,
			kQTMovieAudioExtractionAudioPropertyID_AudioStreamBasicDescription,
			sizeof (asbd), &asbd);
	NSLog(@"   %d",error);
	
	
	NSLog(@"   format flags   = %d",asbd.mFormatFlags);
	NSLog(@"   sample rate    = %f",asbd.mSampleRate);
	NSLog(@"   b/packet       = %d",asbd.mBytesPerPacket);
	NSLog(@"   f/packet       = %d",asbd.mFramesPerPacket);
	NSLog(@"   b/frame        = %d",asbd.mBytesPerFrame);
	NSLog(@"   channels/frame = %d",asbd.mChannelsPerFrame);
	NSLog(@"   b/channel      = %d",asbd.mBitsPerChannel);

	float				numFramesF = asbd.mSampleRate * ((float) GetMovieDuration(soundToPlay) / (float) GetMovieTimeScale(soundToPlay));
	UInt32				numFrames				= (UInt32) numFramesF;
	NSLog(@"numFrames is %d",numFrames);

	UInt32				extractionFlags			= 0;
	AudioBufferList*	buffer					= calloc(sizeof(AudioBufferList), 1);

	buffer->mNumberBuffers = 1;
	buffer->mBuffers[0].mNumberChannels = asbd.mChannelsPerFrame;
	buffer->mBuffers[0].mDataByteSize = sizeof(float) * buffer->mBuffers[0].mNumberChannels * numFrames;
	
	samples = calloc(buffer->mBuffers[0].mDataByteSize, 1);
	buffer->mBuffers[0].mData = samples;
	sampleCount = numFrames * buffer->mBuffers[0].mNumberChannels;
		
	NSLog(@"Filling buffer of audio...");
	error = MovieAudioExtractionFillBuffer(extractionSessionRef, &numFrames, buffer, &extractionFlags);
	NSLog(@"   %d",error);
	NSLog(@"   Extraction flags = %d (contains %d?)",extractionFlags,kQTMovieAudioExtractionComplete);
 	
	NSLog(@"Ending extraction session...");
	error = MovieAudioExtractionEnd(extractionSessionRef);
	NSLog(@"   %d",error);

	NSLog(@"ExitMovies...");
	ExitMovies();

	NSLog(@"Loaded %d samples",sampleCount);

}


