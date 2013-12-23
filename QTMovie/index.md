---
layout: page
title: QTMovie
---



From Apple's documentation:

*The QTMovie class represents both a QuickTime movie and a movie controller. A movie is a collection of playable and editable media content. It describes the sources and types of the media in that collection and their spatial and temporal organization. These collections may be used for presentation (such as playback on the screen) or for the organization of media for processing (such as composition and transcoding to a different compression type). The collection may be as simple as a single file that plays at its natural size for its intrinsic duration, or it may be very complex (with multiple sources of content, rich composition rules, interactivity, and a variety of contingencies).

Just as a QuickTime movie contains a set of tracks, each of which defines the type, the segments, and the ordering of the media data it presents, a QTMovie object is associated with instances of the QTTrack class. In turn, a QTTrack object is associated with a single QTMedia object.

A QTMovie object can be initialized from a file, from a resource specified by a URL, from a block of memory, from a pasteboard, or from an existing QuickTime movie.

Once a QTMovie object has been initialized, it will typically be used in combination with a QTMovieView for playback.*

QTMovie, and its associated classes, requires QuickTime 7 or higher. For QuickTime 6.X and below, you must use NSMovie.

see also: QTMovieCategory

----

**About -initWithData **

If initWithData does not return a valid QTMovie, try this instead:

    
QTDataReference *ref = [QTDataReference dataReferenceWithReferenceToData:filedata
							name:filename MIMEType:mime];
NSError *movErr = nil;
movie = [[QTMovie alloc] initWithDataReference:ref error:&movErr];


Where mime is the original data's mime type, and filename is the original file's filename. If you fetched filedata using NSURLConnection, mime = [response MIMEType] and filename = [response URL]).

----

I find myself often having to debug the load state information to find out what's going on. Here's a handy function for converting a QTMovieLoadState to a string:

    
NSString *StringFromQTMovieLoadState(QTMovieLoadState state)
{
	switch (state) {
		case QTMovieLoadStateError:
			return @"QTMovieLoadStateError";
		case QTMovieLoadStateLoading:
			return @"QTMovieLoadStateLoading";
		case QTMovieLoadStateLoaded:
			return @"QTMovieLoadStateLoaded";
		case QTMovieLoadStatePlayable:
			return @"QTMovieLoadStatePlayable";
		case QTMovieLoadStatePlaythroughOK:
			return @"QTMovieLoadStatePlaythroughOK";
		case QTMovieLoadStateComplete:
			return @"QTMovieLoadStateComplete";
	}
	return @"Invalid state";
}

