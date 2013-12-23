---
layout: page
title: QTMovieAndCoreData
---




It wasn't clear to me early on, but when I call [QTMovie movieWithFile: error:], it's only pointing to the movie data on the drive, and not actually collecting the data or caching it... if I add it to my file in a CoreData document based app, and then try to save it in the file and take it to another machine, it's going to sit there and tell the user "I can find it! Oh noes!" With CoreData document-based apps, I know that I can't use bundles, so it's tough to store the file with the saved document.
Is there a way I can store a QTMovie instance, and pack the media data with it when it saves? Which object is the actual data of the movie: QTMedia, or QTTrack?

----
Read the file into an NSData, then use     +movieWithData:error: to read it into a QTMovie after you get it out of CoreData?

----

That's what I'm already doing (since you have to convert objects to something that CoreData can handle... in this case, binary). The problem is, when I use that method to get the QTMovie back from my data, QuickTime pops a box up saying something to the effect of "looking for data for filename.ext"... where it names the original file that the movie was created from.

----
I don't think it's what you're doing. Your code does not do this, and you don't describe doing it. I believe you misunderstood my suggestion. My suggestion was this:

    
// put file into CoreData
NSData *fileData = [NSData dataWithContentsOfFile:path];
// store fileData in CD store

// load movie
NSData *movieData = [self _getMovieDataFromCoreData];
QTMovie *movie = [QTMovie movieWithData:movieData error:&error];


Note that I am *not* storing the QTMovie in CoreData in any way. I am storing the raw file data, then creating a new QTMovie each time using this raw data.

----

Ah. Okay, you're right; I misunderstood you. I'll have to try that. Thanks!

----

Please don't store raw data of huge sizes (like movies or even images) in a CD database. Store it in files and reference them. Your app will be a *lot* faster.

----

The problem is, I can't do that if my user wants to save the video or images with the document and take it to another computer, because CoreData document-based apps don't support packages. Any other possible solutions for such a problem?

----

At least gzip your data before putting it into the database.

----

Video formats are already compressed. gzipping the data would be a big waste of a lot of user time.

----
If you use SQLite and follow the documentation's suggestion to create one-to-one relationships between your BLOBs and their associated "parent" entities, you should be fine.

----
I have a scenario where I'm storing a QT movie file in Core Data and that seems to be working fine.  The problem I have is now I'm trying to actually view the movie from a QTMovieView.  The QTMovieView's "Movie" binding is connected to my core data source with the correct keypath and 'NSUnarchiveFromData' set for Value Transformer, but all I get is a black box.  I've tried with the Value Transformer as blank, but that doesn't get me anywhere.  I've also tried to create a simple button and have it send the play: message to the view and doesn't have any effect.  To load the movie file I'm using NSData dataWithContentsOfFile:path options:0 error:&error].  I've verified the size of the NSData object after it is loaded and it matches, and I've also made sure the size of my persistent document increases by at least the size of the movie file.  I've also verified that the movies play in QuickTime player, and they do, and the attributes match when loaded by my app.

Any ideas?

