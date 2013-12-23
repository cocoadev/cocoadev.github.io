---
layout: page
title: HowToDetectUserScreamingIntoMic
---

Hey guys,

I basically need to detect the user screaming into the microphone (or just any loud noise), but I really don't have any experience with audio programming, so I don't know any of the audio terminology. I'm using Michael Thornburgh's MTCoreAudio framework (an objective-c wrapper for CoreAudio). But like I said, I don't know any of the terminology, so I'm having a hard time finding my way through the code.

Could anyone point me in the right direction for what I'm trying to do? Even if you tell me in terms of the CoreAudio framework, it should give me a general idea of what to look for.

Thanks!

----

This seems a very broad question, so I'll give you a very broad answer. The sound pressure level (SPL) at the microphone is more or less linearly converted to the amplitude of the samples. The ear perceives SPL logarithmically and with a variable frequency response however, so if your aim is to detect something in the way a human might, you may need to take this into account. If it's just any old loud noise, you can detect this by looking for a series of sample values above a certain threshold for a certain period of time - maybe averaged to get rid of the odd spike or dip. I believe Core Audio represents samples as floating point values, so you'd simply take the absolute value of the sample, integrate it over the period of time of interest (say half a second) and compare it with a threshold value. If it's greater, trigger your action. --GC

----

Thanks -- I now at least have a general idea of what I need to do. But for the life of me, I can't figure out how to get sample values; I can set the sample rate, but I can't figure out how to get the actual sample values. Am I missing something simple, or there no easy way to do this?

----

The link at the bottom of the MTCoreAudio page talks about this.

----

Aha, that link was really useful. From what I gather, AudioBuffer->mData is the sample value. However, it's defined as void*, and I can't figure out what data type it's actually pointing to. When I cast it as Float32, it prints as 0.0000, but a quick "if" statement shows that it's not null. What data type is mData, and how can I extract the actual data from it?

----
The data type depends on what you get fed, the AudioBuffer contains information on the type.

As for your 0/if thing, I have no idea what you're saying. A float value which is 0.0000 will not pass an if test. Testing the pointer value is completely nonsensical.

----

The AudioBuffer contains the type info? All I see in AudioBuffer is mNumberOfChannels, mDataByteSize, and mData. Is it mDataByteSize? If so, how do I "translate" it? The value of mDataByteSize is 4096.

Sorry, I should have elaborated about the if thing better. All I meant was that I checked mData with     if (mData == NULL) .

----

While I haven't played with this myself, I feel fairly sure that samples are 32-bit floats. Thus each sample is 4 bytes. The mDataByteSize is the number of bytes in the buffer, so the number of samples will be this / 4. Possibly the easiest way to handle this is to cast the AudioBuffer->mData pointer to a float[], then you can simply access it as an array:      float samples[ ] = (float*)(buffer->mData);  int numberOfSamples = buffer->mDataByteSize / 4; float aSample = samples[i];  etc. (Note I had to add a space between the square brackets to make it obvious that's what it is - in real code you wouldn't have that). Note that a value of 0.000 is reasonable - it simply means that the sample value is zero, which it will be for no input.  --GC

----
Sorry, the data type information must be somewhere else. I know CA provides it, though.

For things which aren't dealing directly with raw hardware it is probably safe to assume it will be four-byte float values, though.

----

From "Introduction to Core Audio": *For PCM devices, the generic format is 32-bit floating point, maintaining a high resolution of the audio data regardless of the actual physical format of the device. This is also the generic format of PCM data streams throughout the Core Audio API.* (http://developer.apple.com/documentation/MusicAudio/Reference/CoreAudio/core_audio_intro/chapter_2_section_3.html ). --GC

----

Thanks. I tried     float samples[ ] = (float*)(buffer->mData);, but I just get "error: invalid initializer". I must be doing something really stupid, but I can't figure why I'm getting that error... my C is pretty rusty. :( Anyone know what I'm doing wrong?

----
You declare a pointer to float with     float*, not     float[].

----

I get the feeling you have a Java or C# background. That's perfectly fine, but Objective-C is not like those languages--it's a thin layer on top of plain-old C, with a lot of Apple-specific functions on the side. If you're not as comfortable with C as with Java or C#, or if you just need to shore up on your syntax, I recommend taking a look through *The C Programming Language* by Kernighan and Richie (the Bible of the C language.) It's an oldie, but still relevant. After that, any number of CocoaBooks will be helpful to you.

----

My bad. float[ ] and float* are equivalent. However, my C is rusty in that you can't dynamically assign to float[ ], though float [ ] = {2, 3, 4, 5, 6}; would be perfectly fine. Just one of those non-orthogonal glitches in the language I guess. Anyway no matter, declare it as float* - you can still access it as an array since an array and a pointer are the same thing in C. --GC

----

Argh... I tried using     float* samples = (float*)(buffer->mData);, but I get "Command /Developer/Private/jam failed with exit code 1". I've tried Googling this error, but I can't seem to find what the error means. Does anyone have any idea what could be wrong? Here's the part of the build transcript relevant to Jam:

    JamToolExecution Recordotron
    cd /Users/eamonford/Desktop/Recordotron
    setenv ASCII_OUTPUT_ANNOTATION YES
    setenv ENABLE_APPLE_JAM_EXTENSIONS YES
    setenv ENABLE_APPLE_JAM_OUTPUT_ANNOTATION YES
    setenv GROUP eamonford
    setenv NATIVE_ARCH ppc
    setenv USER eamonford
    /Developer/Private/jam -d1 -j1 JAMBASE=/Developer/Makefiles/pbx_jamfiles/ProjectBuilderJambase JAMFILE=/Users/eamonford/Desktop/Recordotron/build/Recordotron.build/Development/Recordotron.build/Recordotron.jam build ACTION=build _DEFAULT_GCC_VERSION=4.0 GCC_VERSION_IDENTIFIER=4_0 CONFIGURATION=Development BUILD_STYLE=Development CPP_HEADERMAP_FILE=/Users/eamonford/Desktop/Recordotron/build/Recordotron.build/Development/Recordotron.build/Recordotron.hmap SRCROOT=/Users/eamonford/Desktop/Recordotron OBJROOT=/Users/eamonford/Desktop/Recordotron/build SYMROOT=/Users/eamonford/Desktop/Recordotron/build DSTROOT=/tmp/Recordotron.dst

BTW, to the guy who posted about the C book: thanks for the suggestion, but I actually already have a C programming book. I actually don't come from a Java or C# background at all; I learned C first, and then I learned Objective-C, but I haven't really been programming in C at all since I learned Objective-C. So that's why my C is rusty. :) But you're right, I probably should brush up on my C, just for situations like this...

----
Upgrade to a native Xcode target. And hit up Apple for a lot of CoreAudio sample code.

----

Oh, whoops... forgot to do that. But now I'm getting "Command /usr/bin/gcc-4.0 failed with exit code 1". Same error, only with gcc instead of jam. Anyone know what "exit code 1" means?

----

It means that either GCC's main function returned 1, or GCC called exit(1);. Check Xcode's compile log--it will show you the error that caused the failure.

----
I would like to suggest that this MailingListMode page has gone way off the tracks and further discussion would be better suited to the xcode-users list.

----
You're right, this is going way off topic. Thanks for all the help you guys have given me; I now have a better understanding of Core Audio. :)

BTW, for posterity: I asked about that error in the xcode-users list, and found the problem: the compiler was treating warnings as errors, and so it didn't like the fact that I had an unused variable.

