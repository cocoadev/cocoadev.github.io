---
layout: page
title: AudioExtractionBuffering
---



**Goal**

I'm trying to extract raw PCM audio from any given QuickTime-compatible media for later waveform plotting. Specifically, I need the number of samples to be equal to the width (in pixels/points) of my waveform view. This requires dividing the total samples by the number of pixels, then averaging the values of each "slice", storing the average in a buffer for drawing.

The overall intent is to only resample the file if the view's width is changed (not likely to happen often in the interface in this particular app), and to cut down on the buffer size requirements dramatically. The data can/should be down-sampled to a low quality, using mono only since that's all that's needed for a simple waveform plot used only for identifying sound in a large, mostly-quiet media clip, the track view of which cannot be zoomed. Precision nor high quality are required or desired.



**So Far No Good**

I have a "working" version of this that takes its cues from some random examples I've found online, but the trouble with this is that it allocates a buffer sized for all samples in the entire file. It stores all samples and redraws the waveform without This is fine for a minute or so of audio, but for hour-long home movies, etc. ... ouch! It is truly a resource hog.

Apple's SimpleAudioExtraction sample code seems to do a better job. It takes smaller "slices" of audio and scheduling them for playback, which keeps the buffer requirements down to a minimum:

http://developer.apple.com/samplecode/SimpleAudioExtraction/index.html



**The Rub**

My problem is, as a self-taught developer with mostly Cocoa and Java experience, I'm at a complete loss as to how to adapt Apple's approach to fill a buffer sized to 'n' with averaged chunks of raw PCM data.

As there are few examples out there that address this general topic, I thought this might be a useful addition to the wiki.

Any takers?



----

**Discussion**


Hey, what is n? The number of samples to be represented by one vertical line in the wave view I assume. Or is n the width of the view? Each audio clip will have a different samples per pixel ratio so your buffer will be of varying sizes depending on the clip. If you have a file with 1000 samples in a view a 100 pixels across, you need to load at least 10 samples from the quicktime file (unless you want to get really fancy) to generate one vertical line of the waveform view. In a clip of 5000 samples, you need to load more at a time. You may want to load many vertical lines worth o
b9f
f samples for shorter files. Then just do it. Incrementally get data, do the calculations, and record resulting 'peak' data in a buffer, desk, etc., then use the data to draw. I think quicktime can handle itself, ie. it would not complain of odd sample count requests. If quicktime does complain (I really don't know quicktime at all), you need to add a bit of buffering yourself, but the principle remains the same. 

I hope this helps, but perhaps you could rephrase the question?
-JeremyJurksztowicz

----

Well, *n* is the width of the view or total number of samples I wish to retain in memory for plotting. I'm fully familiar with the fact that each audio clip will have its own sample count. Not just due to length but sample rate. I also know how to divide. ;-) The need for buffers was stated not only in the title, but the body, so that's covered, too. :-D

It's the actual mechanism for slicing down the media piece by piece and moving things around from buffer to buffer, while performing the averaging. I simply can't wrap my mind around it. As the title suggests, this is the crux of my problem: how to tackle the actual buffering. I assume I need to create a "slice" buffer sized to fit the number of samples per pixel. I assume I also need a separate buffer sized to *n*, where *n* is the number of pixels (therefore, the number of averaged samples). I need to iterate through the samples *slice-size* samples at a time, perform the average on those samples, then store the 'averaged sample' into my final buffer. So ... I know the name of the city, now I just need directions and so help me God if someone tells me "Ya' can't miss it!" ;-)

----

If I've followed you correctly, what you want to know is how to downsample one buffer into another. Whether the src buffer is audio and the dest buffer represents pixels is not all that relevant, I think. So here's a stab at some code that should do it. It's untested and may not be correct, but the gist of it will hopefully help you (others or yourself may care to amend it to fix any bugs that you find!). I've commented it so that you should be able to follow the intention of the logic, even if it doesn't work right off the bat. I'm assuming the buffers contain float values but it's easy to change for any data type.  --GrahamCox

    

void		downsample( const float* srcBuffer, float* destBuffer, const unsigned srcLen, const unsigned destLen )
{
	// copy contents of <src> to <dest> where dest is smaller than src. Values in src are averaged as needed.

	if ( srcBuffer == NULL || destBuffer == NULL || destLen > srcLen || destLen < 1 )	// sanity checks
		return;

	unsigned		i, n;
	float			sample;
	const float		*p, *sp = srcBuffer;
	
	// iterate over the smaller buffer as this is the shorter loop
	
	for( i = 0; i < destLen; ++i )
	{
		// Each block of the src that needs to be averaged will contain src/dest samples, but we need to deal with rounding as we can't have a fraction of a sample.
		// Thus compute a variable integer block length such that over the whole length of the buffer the block length averages to this ratio. Every src sample
		// contributes something to the destination.
		
		p = sp + ((i + 1) * srcLen) / destLen;				// p points to the end of the next block to average
		n = p - srcBuffer;			// n is the length of the block, always at least 1
		
		for( sample = 0; srcBuffer < p; sample += *srcBuffer++ );	// add together <n> src samples, <src> ends up pointing to start of next block
						
		destBuffer[i] = sample / (float)n;				// write the dest sample which is the average of <n> src samples
	}
}




Update: posted an improved (and tested!) version which actually works. Hope this helps! Another thought - you might find that using every sample in the original buffer is overkill. For performance you might consider taking only every fourth (say) instead, which would involve a small mod to the code above to accomplish. Then again, I just measured this for a src buffer of 3MB, and it takes about 12mS on a 2GHz MacBook, so I'd say it was pretty fast.   - --GC

----
Be wary of optimizations such as taking only every Nth sample. Audio data can be highly regular at the small scale and you can end up with very strange sampling artifacts which will tend to emphasize or de-emphasize frequencies which are the "right" harmonic of your sampling interval. This should be left as an absolutely last resort if all other measures to obtain needed speed fail. On a modern computer the cost of such averaging is pretty light, and the benefit gained from skipping small pieces of a stream is almost nil given how the memory systems work, so it's probably not worth it. Not meant to be a criticism, just some further light to shed on the subject.

---- On reflection, I agree, this wouldn't be a good idea. My feeling was that this could get slow over a large audio file, but it turns out it's not (which makes me wonder why some implementations I've seen take so long!) The above code executes in linear time, approximately 5nS per sample on the machine quoted, and since you'll be doing this downsampling only rarely, you should find that for practical purposes it's effectively instant. --GC

----
If you're concerned about performance vs. accuracy, why not start at both ends and perform M averages?  I could see this being done as a weighted average that would have efficiency around O(M^2) for M = f(N) | O(f) < O(N).

----
3MB of raw float-based audio is not a lot; for stereo 44.1kHz data it's about nine seconds. You're outrunning playback by a factor of about a thousand, and it's pretty easy to come up with audio files which will result in a significant delay. (I have seen people edit 60-hour audio files, prepare for the worst.) In addition, apps often do more complicated things with the data, such as computing min/max values and RMS averages, which may slow it down more. But still, OptimizeLater, and add support for incremental waveform display if the delay is unacceptable rather than trying tricks like a small stride. My uninformed guess is that this technique is going to be primarily constrained by memory bandwidth, and small strides won't help out at all with that.

----
So what *is* the solution for the original problem? Allocating a huge float buffer for an hour-long media clip at 44.1kHz is - hands down - the bottleneck, not the extraction itself. In a previous post, someone said OptimizeLater and the OP said, "it *is* later." I'm in exactly this position. As I said, I have a working solution, it's just unusably slow for the average media clip this app is likely to handle. It's time to OptimizeNow ...

----
Something like a mipmap in OpenGL should allow you to do most of the calculation just one time, then allow for quick rendering once it's been calculated.

----
With respect, that seems like swatting a fly with an ICBM. Averaging (or peaking if that's indeed the better approach) the samples at (total-samples / view-width) at a time and storing only those averaged (downsampled, thanks GC) floats in memory seems like a perfectly reasonable approach and I haven't really seen anything that has convinced me that's a bad path. Of course, I may be misunderstanding what I'm reading above, and if so, please clarify. The time-sink, after using the profiling tools, is allocating several million floats to hold all the samples in memory. IMO, that's completely unnecessary when I'm only ever going to be drawing, say, 400 lines unless the window is resized, at which point a new extraction / downsampling would take place when the resize is complete. All I'm interested in is a way to hold the bare minimum in memory that I need to plot a reasonable waveform. Believe me, in a 400-pixel-wide, non-zoomable view, an hours worth of audio isn't going to look exact anyway. ;-) That's **perfectly okay**. Consider iMovie's audio tracks and their waveforms, but non-zoomable. That's all I need.

----
You're right, I was assuming a view which could zoom. If you can't zoom and you're ok with taking a hit when you resize then of course you don't need to do anything that heavy. If you prefer a snappy resize you can decide on some insane maximum width, like 30,000, then cache that and recalculate your "real" waveform based on the cache, which should be really quick.

----
I like that idea far better. :-) So does the above example code lend itself to this approach? I've gone over it a few times and it appears to be what I need (thanks, GC), but I'm having trying to imagine how to "plug it in" to an extraction session.

----

One possibility is to do it in two "passes". You could downsample the original audio into, say, a 1MB snapshot, then use that to generate the pixel plot of the desired size. Keeping the 1MB buffer around is much more feasible than keeping the original entire file. You still have the problem of the initial downsample which will have to be done in a series of chunks. I'm not familiar with Core Audio or QT, but presumably at some point you are passed a chunk of audio that has been decoded. There's no reason you can't use the above code (modified to deal with the decoded data type in the original source buffer without conversion - floats were just an example) in a series of calls that gradually build the 1MB buffer from the series of chunks - the destBuffer parameter isn't required to be the beginning of the buffer, it can be anywhere. What's not clear (to me) is whether you can force QT or CA to decode the audio at some arbitrary high speed rather than some fixed playback rate, which would of course be necessary to do this in any sort of realistic timeframe. --GC

----
My two cents: say what?
You really don't want to process your audio view in a realtime audio thread. So spawn your own thread if you want, or just do it in the main thread.
What's all this talk of downsampling? I think what you want is the peak values, ie, the minimum and maximum values in a range of samples, how can you draw a vertical line with only one value? If you downsample a sine wave to one sample, it's value will be zero, obviously not what you want to plot on the display.
You say it so well in english:
**
I need to create a "slice" buffer sized to fit the number of samples per pixel. I assume I also need a separate buffer sized to *n*, where *n* is the number of pixels (therefore, the number of averaged samples). I need to iterate through the samples *slice-size* samples at a time, perform the average on those samples, then store the 'averaged sample' into my final buffer
**
So unless I write it for you, I cant help much more. Here is some pseudocode off the top of my head...

    
const double samplesPerPixel = sampleCount/viewWidth;
const double sampleFractionsPerPixel = modf(samplesPerPixel, NULL);
double collectedSampleFractions = 0.0;

for(unsigned i = 0, j = 0; i  < sampleCount && j != viewWidth; ++j)
{
    unsigned readAmt = (unsigned)samplesPerPixel;
    collectedSampleFractions += sampleFractionsPerPixel;
    if(collectedSampleFractions >= 1.0)
    {
        readAmt += 1;
        collectedSampleFractions = modf(collectedSampleFractions, NULL);
    }

    float * buffer = bigEnoughBuffer(readAmt);
    readAmt = readSoundFile(buffer, readAmt);
    _soundPeaks[j] = getMinMax(buffer, readAmt); // Minmax values, maybe struct { float min, max; };
}


Obviously I left out a lot of detail. If you want to load big sounds smoothly, you can do one loop iteration, update the 'peak' buffer, and update the display, before you go on to the next loop iteration. This way the wave will slowly be drawn on screen, while not stalling the main thread. Just writing this sorta-code fragment made my head spin a little, so I understand the frustration of juggling all these variables, especially when dealing with multiple threads and all. Good luck! -JeremyJurksztowicz

