---
layout: page
title: MovieRect
---

I have some troubles with this source: http://cocoa.karelia.com/AppKit_Classes/NSMovieView_subclas.m
I get two warnings when I compile:

*NSRect viewRect = [super movieRect]; //'NSMovieView_subclass' may not respond to '-movie'
*Movie qtMovie = self movie] [[QTMovie]; //'Controller' may not respond to '-movieRect'

I don't know what's wrong!

*is Controller a NSMovieView subclass?*

----

Are they jut warnings or does it actually fail when you run it?

----

Controller is a NSMovieView subclass, and they are just warnings, but it won't work.

----

You know what is funny here? We have the first line:
    
NSRect viewRect = [super movieRect]; //'NSMovieView_subclass' may not respond to '-movie'


Which refers to NSMovieView_subclass, but its rather obvious that this line of code doesn't have movie in it. And we have this line:
    
Movie qtMovie = self movie] [[QTMovie]; //'Controller' may not respond to '-movieRect'


Which refers to Controller, but it doesn't have movieRect in it. So obviously the build warnings are associated to the wrong lines and the lines are in completely different classes than each other.

----
You know what... the warnings are on wrong line this is what it looks like:

*NSRect viewRect = [super movieRect]; //'Controller' may not respond to '-movieRect'
*Movie qtMovie = self movie] [[QTMovie]; //'NSMovieView_subclass' may not respond to '-movie'

What's wrong now.
*One more question: This is how you subclass. Right?

    
#import "Controller.h"

@interface NSMovieSubclass : Controller {

}
@end


Sorry about the lines!

