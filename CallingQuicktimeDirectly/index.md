---
layout: page
title: CallingQuicktimeDirectly
---



Here is some sample code to load a movie (anything that QuickTime supports) and start playing it (only audio though).

Please note, NSMovie and NSMovieView are obsolete in favor of QTMovie.

    
- (void)setMovie:(NSMovie *)movie
{
	if (_movie != movie)
	{
		[_movie release];
		_movie = [movie retain];
	}
}

- (void)setTimer:(NSTimer *)timer
{
	if (_timer != timer)
	{
		if (_timer) [_timer invalidate];
		[_timer release];
		_timer = [timer retain];
	}
}

- (void)resetTimer
{
	NSTimer *timer = [NSTimer scheduledTimerWithTimeInterval:0.5 target:self selector:@selector(checkForEndOfMovie:) userInfo:nil repeats:YES];
	[[NSRunLoop currentRunLoop] addTimer:timer forMode:NSEventTrackingRunLoopMode];
	[self setTimer:timer];
}	

- (void)play
{
	[self setMovie:[[[NSMovie alloc] initWithURL:[NSURL fileURLWithPath:self track] location byReference:YES] autorelease]];
	if (_movie != nil)
	{
		StartMovie([_movie QTMovie]);
		[self resetTimer];
	}
}

- (void)checkForEndOfMovie:(NSTimer *)timer
{
	MoviesTask([_movie QTMovie], 500);
	
	if (IsMovieDone([_movie QTMovie]))
	{
		[timer invalidate];
		
		// notify delegate (update UI?)
		SEL sel = @selector(trackPlayerDidEnd:);
		if ([_delegate respondsToSelector:sel])
			[_delegate performSelector:sel withObject:self];
	}
}


The timer (which checks for the end of the movie) is added to the current run loop, so that when the window is resizing, and other GUI stuff is happening, the movie keeps playing. This is important if you want to have smooth playback. The QuickTime functions used are StartMovie(), StopMovie(), IsMovieDone() and MoviesTask(). MoviesTask is used to keep playing the movie.

