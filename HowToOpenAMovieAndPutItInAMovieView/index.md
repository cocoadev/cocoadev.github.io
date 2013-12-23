---
layout: page
title: HowToOpenAMovieAndPutItInAMovieView
---

This article shows you the code that will open a movie file in the finder, assign the movie to an NSMovie, and then put this movie into an NSMovieView.

In addition to this code, you need to create an outlet for an NSMovieView (named myMovieView) in your header file.  You also need to put this code in a method that you attach to perhaps the "Open" item in the "File" menu.

    
//Declare op as a pointer to an NSOpenPanel.
NSOpenPanel *op;

//Create, initilize, and assign an NSOpenPanel to the pointer op.
op = [[NSOpenPanel alloc] init];

//Open an Open panel and record the file the user selected.
[op runModal];

//Assign the movie file to the NSMovie pointer named movie.
NSMovie *movie=[[NSMovie alloc] initWithURL: [op URL] byReference: YES];

//Put the movie into the NSMovieView.
[myMovieView setMovie:movie];

//Release the objects you allocated above.
[movie release];
[op release];


Back to HowToProgramInOSX

