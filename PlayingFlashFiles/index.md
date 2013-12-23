---
layout: page
title: PlayingFlashFiles
---

Hey again.

I'm working on an application which plays flash files... in fact, it's basically its whole purpose to play flash files.  I tried to do it through quicktime, but it always returns YES for isPlaying... whether or not the flash file is playing.  This isn't true for the MOV-type of files.

So, I'm wondering.  How hard would it be to integrate the actual flash plugin into an application?  Is there any sort of documentation on this sort of thing?  I'm sure its possible since safari figures out a way to do it :).

Thanks for any help anyone can give.  I do appreciate it.

-Charlie

----

UPDATE:  I fixed this problem.  All you have to do is subclass NSMovieView.  Then, put this in the file

    
-(BOOL)isPlaying
{
    return NO;
}


This will constantly say that it is not playing, through polymorphism, and this fixes the flash file problem.  However, this creates some other problems.  Therefore, you should add a BOOL to the header file, and make a method to set the bool.  Then, let the isPlaying return the bool, and control it manually.

-CharlieMiller

----
So I just tried to use QT to watch a .flv I had saved from the internet and it doesn't work.  Does anyone know if there are any cocoa tools for embedding flash content in your app?  It seems the latest version doesn't play in QTKit, that I can find.  I was also reading WebKitToDisplayFlash and there is a suggestion there to embed it in HTML, then view it in WebKit, since that uses the flash plugin from Macromedia, but I don't know how to write HTML... could anyone give a simple template here for a blank HTML page with one embedded .flv file?  Of course, I would prefer a way to watch it with QTKit if it exists... thanks

----
well, a simple embedded flash file (stripped from youtube) would look like 
    
<embed src="http://www.youtube.com/v/-guOAIcQoxA" type="application/x-shockwave-flash" wmode="transparent" width="425" height="350">  youtube parse code

But that's only for html and therefore, Webkit.  I don't know if quicktime can even import flash files.

