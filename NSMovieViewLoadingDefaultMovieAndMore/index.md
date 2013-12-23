---
layout: page
title: NSMovieViewLoadingDefaultMovieAndMore
---

I'm building an app to edit movies, but it's a small app, so I don't need anything fancy.

1) I have two NSMovieViews which the first one loads a movie chosen by the user through a NSOpenPanel. But I need the movie on the second one to be automatically loaded as the software is executed. I have dragged the movie to the Groups & Files and then put it into a folder called "Images and Movies".
How do I load that movie called "filmDefault" as soon as the app launches?

2) I want to able to select a part of one movie and then paste it into another, so I enabled editing on both NSMovieViews. The problem is that when I copy something from a movie view and paste it into the other one, the app keeps thinking and nothing happens. Then the movie view on which the movie was pasted on stops responding.

I haven't built an action for the Copy/Paste, so I'm using the App's default one. Is that the problem?

----

Anyone, please??

----

For #1, I don't know of a way to set the movie in an NSMovieView from IB alone. You can do it from code, though. Something like this:

    
// in the window controller for the containing window - when
// the nib finishes loading, set the movie
- (void)awakeFromNib
{
  // load the movie
  NSString *pathToMovie = [[NSBundle mainBundle] pathForResource:@"filmDefault" ofType:@"mov"];
  NSURL *  urlToMovie = [NSURL fileURLWithPath:pathToMovie];
  NSMovie * myMovie = [[[NSMovie alloc] initWithURL:urlToMovie] autorelease];
  if (myMovie == nil) {
    NSLog(@"unable to load filmDefault.mov!");
  } else {
    // movieView is an instance variable which you've
    // connected to the right NSMovieView
    [movieView setMovie:myMovie];
  }
}


I don't know about #2, sorry.

----

Thanx dude! It worked perfectly!

