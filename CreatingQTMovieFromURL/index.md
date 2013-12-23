---
layout: page
title: CreatingQTMovieFromURL
---



I am trying to load a movie from a URL and I am having a problem I don't quite understand.  
Here's the code:
    
// movieView is the QTMovieView
[movieView setMovie:[QTMovie movieWithURL:[NSURL URLWithString:@"http://www.myurl.com/movies/1.mov"]
                                    error:nil]];

And the error is 

    *** -[MovController setMovie:]: selector not recognized [self = 0x398180]

I looked on the net a bit, and can't find anyone who has had a similar problem.  I tried looking through Apple's QTKitPlayer example, but it throws an exception when I try loading a movie from a URL.  I double-checked the URL and it is correct.  I also tried with a local URL with no luck.
Any help or insight is appreciated.

Thanks, 

-- ez


----
It has nothing to do with the validity of the URL. The error is telling you that your comment is wrong.     movieView is not a QTMovieView, it is a MovController. Whatever that is (one of your classes, obviously), it does not understand the     setMovie: message, so it dies when you try to send it.

----
I think I'm missing something.  MovController is one of my classes, it extends NSWindowController.  I added a QTMovieView in Interface Builder and linked it to an outlet called     movieView of the QTMovieView type.  I don't get how it sees     movieView as a MovController.

Here is my relevant code in MovController.h
    
#import <Cocoa/Cocoa.h>
#import <QTKit/QTKit.h>

@interface MovController : NSWindowController {
    QTMovieView *movieView;
}

- (void)loadMovie;

@end


And in MovController.m
    
#import "MovController.h"

@implementation MovController

- (id)init
{
    self = [super initWithWindowNibName:@"Movie"];
    return self;
}

- (void)loadMovie
{
    [movieView setMovie:[QTMovie movieWithURL:[NSURL URLWithString:@"http://www.myurl.com/movies/1.mov"]
                                        error:nil]];   
}

@end


I'm sure it's a simple error, but I can't put my finger on it.  

Thanks, 

-- ez

EDIT: Forgot     IBOutlet.  I knew it was a simple mistake...

----
I'm not getting any errors or warnings anymore, but the QTMovieView does not appear to get updated.  When I created the .nib file, I set the     File: parameter to a local movie on my machine.  When I saw that it is working, I removed this parameter (    File: is now blank).  But, whatever URL I pass to     setMovie, it still plays the old local file.  I don't see where this reference is coming from, it's nowhere in my code.  Is there a QT cache or something similar that keeps playing the file?  Why is it not getting updated with     setMovie?

Thanks,

-- ez

