---
layout: page
title: QuickTimeFunctions
---



Hi people...

I wanted to get a QuickTime bar to play a MP3 in my app. So I took a sample code from somewhere (I don't remember, I will add it here if I find it) and I tweaked it to fit in my code.

Now, I want to be able to move the reading head accurately on the bar. For example, move the head to the timer "2:45.12".

I read the QuickTime API documentation, but argh, it's about 4000 pages long. I'm lost. Has anyone did something like this before ? Or anyone that has experience with QuickTime calls. Any hint will be appreciated... Thanks...

-- Trax

----

You might want to take a look at
    
void SetMovieTime (
     Movie            theMovie,
     const TimeRecord *newtime );   


and to get the TimeRecord, try using <code>GetMovieTime</code>.

It's from http://developer.apple.com/techpubs/quicktime/qtdevdocs/APIREF/SOURCESIV/timerecord.htm and http://developer.apple.com/techpubs/quicktime/qtdevdocs/APIREF/SOURCESIII/setmovietime.htm

Here's something that might work if you're using an NSMovie.
    
#import <QuickTime/QuickTime.h>

NSMovie *theMovie; // assume this exists
Movie qtMovie = [theMovie QTMovie];

if (EnterMovies() == noErr)
{
    TimeRecord timeRecord;
    TimeValue currentTime = GetMovieTime(qtMovie, &timeRecord);
    // do something with timeRecord and/or currentTime
    SetMovieTime(qtMovie, timeRecord);
}


Keep in mind that I've never used the QuickTime APIs in my life :).

