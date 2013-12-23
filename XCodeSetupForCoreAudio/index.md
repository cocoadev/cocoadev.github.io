---
layout: page
title: XCodeSetupForCoreAudio
---

I am trying to get started in using core audio. I created a tone generator using HAL functions, which works very nicely. I now need to convert the sounds into MP3 format and write into a file.

I can't get going at all. I never felt so stupid in my life. I can't find the solution to a problem, which gives me the following compile error:

/usr/bin/ld: Undefined symbols: _AudioConverterDispose _AudioConverterFillComplexBuffer _AudioConverterNew _AudioFileClose _AudioFileCreate _AudioFileWritePackets 

The code calls up these six functions but without an underscore. I suspect that I am not importing a needed header file, or the Core Audio framework is not set up properly, but I ran out of things to try.

I could not find any references to header files or General/XCode setup in the Core Audio documentation, though the call that give the problem are described.

I re-downloaded the Core Audio SDK from the Apple web site.

I re-installed Tiger Developer Tool.

I copied the General/MTCoreAudio.framework into the root Frameworks folder.

I did a Spotlight search, but could not find any header files that contained the General/CoreAudio functions I used.

Any suggestions are welcome.

Udo

----

I don't know anything about General/CoreAudio, but I do know that the linker automatically sticks an underscore in front of function names (and maybe even all globals). This might help you in your search. However I would suspect (as a Panther user who's never tried General/CoreAudio) that Apple screwed up when setting up the General/CoreAudio framework. --General/JediKnil
----
When you get undefined symbols, it's because you're not linking against the library or framework which contains them.
----
Link against     /System/Library/Frameworks/General/AudioToolbox.framework and you should be OK

----

Well, I thought if at first you don't succeed, try again. I experimented some more and found the answer. 

To compile the five functions I used, which are listed in the Apple Core Audio documentation, you do not need <General/CoreAudio/General/CoreAudio.h> but <General/SndKit/General/SndKit.h>. Makes sense, or does it?

Udo

----

Here I am again! Progressing, but stuck once more. The General/SndKit framework now links, but I get an error that it can not find lame.h and shout.h header files. The error message is:

*/Library/Frameworks/General/SndKit.framework/Headers/SndAudioProcessorMP3Encoder.h:29:24: error: shout/shout.h: No such file or directory. *

Indeed, the SndAudioProcessorMP3Encoder.h file contained in the framework provided by Apple asks for some files not provided by Apple.

I downloaded the lame-3.96.1 distribution and the icecast-2.2.0 distribution. (The latter contains shout.h.)

I searched the General/XCode documentation, but found no references as how to install non-apple extensions. The instructions included in the distribution are way above my head. They assume the user knows a lot more than I do. I tried various ways to install the header files alone, and the complete package so that General/XCode recognises the header file. I am stuck once more. Again suggestions would be welcome, even if not specific to lame.h, but how to install any third party code into an General/XCode project.

Udo

----

I am making progress by finding titbits of information here and there, but mostly by trial and error. I now installed lame, and Xcode finds the libraries. Here is what I did:

1. Downloaded the "lame-3.96.1" distribution from the Internet.

2. Copied the "lame-3.96.1" folder into the "Documents/temp" folder.

3. In terminal set cd to "lame-3.96.1"  folder

4. Type sudo ( + space)

5. Drag config file from "lame-3.96.1" folder into terminal. Follower by <cr>

6. Type sudo make.

7. Type sudo make install.

8. Copied lame.h from General/SndKit.framework/lame/include to General/SndKit.framework/Headers (Was this necessary?)

Tried to install icecast libraries the same way, but config failed. Got configure: error: must have Ogg Vorbis v1.0 or above installed. I am downloading the Ogg Vorbis distribution at this moment.

Will keep you posted  ----- Udo

----

To keep you posted as promised: I gave up on this approach.

In the meantime apple posted a nice General/CoreAudio example in General/WhackedTV. I can  create sound file in various formats with General/WhackedTV, when my sound generating application and General/WhackedTV are running at the same time, AND I connect the headphone jack to Line In jack with a patch cord on my Dual 1.25 General/GHz G4.

When I have the General/NSLog 2 line, in the example code below, enabled one input and four outputs are listed. However, all my efforts to modify General/WhackedTV to get any of the four outputs into the Record Settings Line Input popup button ended in failure, showing my lack of knowledge of General/CoreAudio. I also noted that when the single item (Line In) in the input popup button is selected, the (unmodified) selectRecordInputmethod ends up in the bail section of the code. This may or may not be related to my problem to get the other sources onto the Record input button.

I added some General/NSLog staments to the selectRecordInput method as follows:

// ---------------
- (General/IBAction)selectRecordInput:(id)sender
{
    General/OSStatus err = noErr;
    General/NSArray * list = nil;
	
    General/NSLog (@"arrived in selectRecordInput"); //  <- General/NSLog 1
    
    BAILSETERR([mChan getPropertyWithClass: kQTPropertyClass_SGAudioRecordDevice
                             id: kQTSGAudioPropertyID_OutputListWithAttributes
                           size: sizeof(list)
                        address: &list 
					   sizeUsed: NULL]);
  			
    if (list)
    {		
        General/NSDictionary * selDict = [list objectAtIndex:[sender indexOfSelectedItem]];
        UInt32 newSel = 
            [(General/NSNumber*)[selDict objectForKey:(id)kQTAudioDeviceAttribute_DeviceInputID] 
                unsignedIntValue];
		
		// General/NSLog (@"objectForKey = %@, [list objectAtIndex:[sender indexOfSelectedItem]]"); // <- General/NSLog 2
        
        BAILSETERR( 
            [self setSGAudioPropertyWithClass: kQTPropertyClass_SGAudioRecordDevice
                                           id: kQTSGAudioPropertyID_InputSelection
                                         size: sizeof(newSel)
                                      address: &newSel] );  // this is line 226 in editor
    }
	    
bail:
    General/NSLog (@"arrived in selectRecordInput - bail:"); //  <- General/NSLog 3
    [list release];
    return;
}

// ---------------

When I select the only input item in the "Line In" popup button,  I get the following log output:

[Session started at 2005-11-28 00:55:08 -0500.]

2005-11-28 00:55:22.487 General/WhackedTV[1106] arrived in selectRecordInput

General/SGAudioSettings.mm:556:-General/[SGAudioSettings selectRecordInput:] ### Err -2195

2005-11-28 00:55:22.488 General/WhackedTV[1106] arrived in selectRecordInput - bail:

Is this intentional considering that there are really no options on the Line In popup button, or is this a bug.

Any hints how to get, for example, the "Line Out" appear in Line Input popup button in General/WhackedTV would be appreciated.

Udo

----
