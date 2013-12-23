---
layout: page
title: CreatingAudioFilesWithQuickTime
---




As a part of the application I am writing, I receieve an audio stream coming to me from a third-party C library. The library has a function call which when called repeatedly provides me with another buffer full of 2-channel 16-bit raw audio. Currently I simply use Core Audio to send this data to the sound device and it works great. The sound plays exactly as expected.

Now I need a way to save this stream to a file. The simple way would, of course, be to simply save the raw sample, but it would be really neat if the user had the ability to choose an encoder such as mp3 or aac and then use it to save the sound. As far as I understand, I should use QuickTime to do this but I have been unable to find any simple documentation on how this is done. I would expect that this would be the typical use so I'm suprised there doesn't seem to be any. Could anyone provide some information on how this is done? 
-- EliasMartenson

----

Using QTKit and *not* streaming the data to the disk, this should be pretty easy. You'd collect the data in memory, slap a basic AIFF header on it, then use QTMovie's     +movieWithData:error: method to create a movie from the data. From there, you can use     -writeToFile:withAttributes: to export to any format QT can export to.

I'm afraid I have no idea how to do this in a streaming fashion, but I hope that this will at least help you get started.

----

Thank you. In this case I have no idea how long the sample is, so I wouldn't be able to collect it in memory. I would, however, be able to use a temporary file. Unless I can figure out a way how to do it for a stream, that is what I will do.

If anyone else have any suggestions how to do it for a stream, I would be very happy to get any tips.

----

Why do you need to know how long the sample is to be able to collect it in memory? Nobody says you had to *pre*allocate all of that memory. 

*He didn't mention preallocating memory - I think he's worried that the clip might be anywhere between a few seconds and a few *hours* long, which is a valid concern. Technically, it should just swap out, but on slower machines, depending on a lot of circumstances, the act of swapping to virtual memory might cause performance problems which could translate to stuttering, etc. I'm no expert, but I'd be concerned with the same problems.*

Good point. And if you had over about 3GB of data, then you'd crash and burn, too....

Of course, writing a 3GB temporary file to the user's hard drive is not ideal either. Streaming the encode really would be a lot better.

----

Anyone know how the *QTMovieExportSettings* attribute is supposed to work for     writeToFile:withAttributes:? The documentation says that its "an object of type NSData that contains an atom container of movie export settings". But unfortunately it's not that easy to get custom settings via QTAtomContainers.

----

I've been trying to make this work as well. I have code for getting the QTAtomContainer, but my export code still doesn't work. I posted to the Quicktime-API list to see what I'm doing wrong. If I get it fixed, I'll post back here.

Update: two very kind people from Apple got back to me and solved my problem for me. I've put together a complete list of instructions with code on QTMovieExportSettings.

----

As other people have realised, I have no idea how long the sample is. In some cases it will simply run until the user interrupts it. Because of this storing the sample in memory is notpossible. A temporary file will work, of course, but it may take a lot of hard drive space.

I'm starting to get the impression that this may actually not be possible. Has anyone suceeded doing anything like this?

-- EliasMartenson

----

It certainly is possible. First of all, are you getting sound data in realtime? That is, are you recording the same thing that is coming out of your default device?
If this is the case you are probably receiving data in a time-constrained thread. This means that you have to be very careful and not do any blocking operations during streaming. Since most disk related actions block for at least a while, this means you will have to pipe the audio buffers to a new thread, one that is not time constrained, and write the buffers chunk by chunk. There is a lot of discussion concerning techniques to do this on the apple coreaudio mailing list. As far as I can tell, there are no satisfactory cocoa solutions to this. This is partly because cocoa's memory management system does not play well with realtime threads (due to autorelease pools). You are better off using the C/C++ interface to a library like QT, or LAME, or what I like, libsndfile. Google Coreaudio swiki for a bunch of info. - Jeremy Jurksztowicz

----

I am getting the sound data in realtime. The sound actually comes out of a C-library that emulates the Atari ST sound chip. I am currently playing this sound through the speakers but I am also adding the ability to save the stream to a file. I could, of course, use some open source mp3 encoding library, but it would be nicer to be able to use QT to encode to any format for which support is installed.

I am obviously going to buffer the output in case the encoding is slower than the replay, but only to a certain limit. If you have a too slow machine, you'll have to expect that the sound will not be right. However, it is expected that most users will actually turn off replay while encoding the output since most users machines can actually encode faster than real time.

-- EliasMartenson

----

Check out the play buffered sound file at www.snoize.com. It uses a high priority thread to read from disk, and then uses a ring buffer to pass the data to the coreaudio thread. You can use the same technique in reverse to write to disk. Note that the example is in Cocoa, and uses some sophisticated techniques to change mach thread priorities, and does some weird mach memory mapping, but essentially the algorithm is rather simple, it boils down to (in C++):

    
OSStatus coreAudioCallback (AudioBufferList * buffs, UInt32 frames, /*all the other stuff*/)
{
    if(ringBuffer.room() >= frames) {
        ringBuffer.copy(buffs, frames);

        sempahore_signal(sem);
    }
    else return -1; // Or some overflow error code...
}

void writeDiskThreadCallback ( )
{
    // Of course you will want an exit flag somewhere in this block...
    while(semaphore_wait(sem, 0, 100000)) 
        continue;

    unsigned frames = ringBuffer.framesWaiting();
    float * buffs = malloc(frames*sizeof(float)*ringBuffer.channelCount());
    ringBuffer.interleaveCopy(buffs, frames);

    // Insert preffered API here, I like libsndfile as I find QT quite complicated (you get what you pay for!)
    sf_write_float(soundFile, buffs, frames);
    free(buffs);
}


This is the jist of it. Just remember to NOT use cocoa anywhere in the coreaudio callback, it is ok to put it in the disk writing thread, just remember your autorelease pools. Also prefer the mach semaphore code to pthreads sempahores as mach semaphore_signal is guaranteed not to block.

- Jeremy Jurksztowicz

----

Writing a file can be done pretty easily using the AudioFile API in the AudioToolkit framework, part of CoreAudio. Download the CoreAudio SDK from ADC and look in the sample code -- there are some examples there that write audio streams to AIFF files. It should take you less than a page of code in all, I think.

- Jens Alfke

