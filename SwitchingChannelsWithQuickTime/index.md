---
layout: page
title: SwitchingChannelsWithQuickTime
---

Hi all,

Is there a way to switch channels using the General/QTKit framework. What I mean is to get the left channel output into the right speaker and vice versa.

I have been going through the documentation for General/QTKit but I don't seem to be able to find anything. 

I have even looked into the General/MTCoreAudio class, the wrapper around Core Audio, but I can't locate anything. 

A little off topic --> I have constructed an application that does sound playback using General/QTKit, but the volume control(for both channels) is managed by General/MTCoreAudio. I think its really easy doing it this way rather than having the sound playback also through General/CoreAudio. Can anyone suggest if there are any flaws with this technique?

Any help is appreciated -- Ron.



----
First of all are the channels interleaved?
In an interleaved stream the left channel preceeds the right, purely by convention, ala: L1, R1, L2, R2, L3, R3...
General/QTKit is probably not designed for realtime processing. If you want to 'flip' channels you need to insert a filter in the audio graph. There are several ways of doing this, perhaps there is a mechanism in General/MTCoreAudio to put a pre/post render callback?
If you intend to do other 'modifications' of the sound stream then I suggest you dive into General/CoreAudio and get it over with.
-Jeremy Jurksztowicz

----

Thanks a lot.

I think that the sound has two channels in it separately, if I am understanding it right. The sounds have a speech and a noise(general static like noise) in them, with the noise coming through the left speaker and the speech coming through the right one.

Could you please please guide me to a good source of documentation for Core Audio. I have been trying to comprehend it but with no luck. The documentation on the Apple website for Core Audio seems to start off at a basic level, but then just jumps off and I have absolutely no clue as to what's going on. Thanks to General/MTCoreAudio, I was able to add system level volume control to my app. 

I have looked in a number of different places for good information on Core Audio, but to no luck. I am willing to put in the time required to access Core Audio, but a starting point isn't visible anywhere. 

Any guidance is thoroughly appreciated -- Ron.

----

I've been going through the mailing lists and stuff and have come up with this piece of code to do the flipping. There is a button on the user interface which on clicking invokes the flip action  method. However, when I run the program and click on flip I get the following error and the application quits. I get no errors or warnings on compilation. The first line is from General/NSLog()

2005-07-27 13:52:44.522 General/AppMake[12395] Tracks: (<General/QTTrack: 0x39cd80 Size = { 0, 0 }, General/QTMedia = 0x3df640>)

General/AppMake has exited due to signal 10 (SIGBUS).

The code I added is below 

In the interface file(General/MyDocument.h) I added

    
//Instance variables
General/AudioChannelLayout *trackChannelLayout;
UInt32 trackChannelLayoutSize;
General/NSArray *tracks;
int channel;

//Prototypes
General/OSStatus setTrackLayout(Track track, UInt32 inLayoutSize, General/AudioChannelLayout* inLayout);


In General/MyDocument.m I added
    
- (General/IBAction)flip:(id)sender
{
	General/OSStatus err;
	channel++;
       //theSound has been initialized in another method by, theSound = General/[[QTMovie alloc] initWithFile:theFileName error:nil];
	tracks = [theSound tracks];
	General/NSLog(@"Tracks: %@", tracks);
		
	trackChannelLayout->mChannelLayoutTag = kAudioChannelLayoutTag_UseChannelDescriptions;
	trackChannelLayout->mChannelBitmap = 0;
	trackChannelLayout->mNumberChannelDescriptions = 2;

	if((channel % 2) == 0)
	{
		
		trackChannelLayout->mChannelDescriptions[0].mChannelLabel = kAudioChannelLabel_Right;
		trackChannelLayout->mChannelDescriptions[1].mChannelLabel = kAudioChannelLabel_Left;
	}
	else
	{
		trackChannelLayout->mChannelDescriptions[0].mChannelLabel = kAudioChannelLabel_Left;
		trackChannelLayout->mChannelDescriptions[1].mChannelLabel = kAudioChannelLabel_Right;
	}	
	
	trackChannelLayoutSize = offsetof(General/AudioChannelLayout, mChannelDescriptions[2]);
	
	err = setTrackLayout(General/tracks objectAtIndex:0] quickTimeTrack], trackChannelLayoutSize, trackChannelLayout);
}

[[OSStatus setTrackLayout(Track track, UInt32 inLayoutSize, General/AudioChannelLayout* inLayout)
{
	General/OSStatus err = noErr;

	if (!inLayout || (inLayoutSize == 0) || (track == nil)) 
	{
		err = paramErr;
		goto bail;
	}

	// Setting the track layout
	err = General/QTSetTrackProperty(track, kQTPropertyClass_Audio, kQTAudioPropertyID_ChannelLayout, inLayoutSize, inLayout);
	
	bail:
	return err;
}


What I assumed (in the newbie way) was that each time the user clicks on flip the channels will switch -- but of course, that didn't happen and I got the errors I mentioned above. 

If someone could kindly tell me where I went wrong it would be of tremendous help. I have already had a hard time figuring this much out and I honestly don't know what's wrong. Some things that must be obvious to most of you is(as you must be seeing) not to me.

Again, any help is really appreciated -- Ron.
