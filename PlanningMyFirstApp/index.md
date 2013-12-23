---
layout: page
title: PlanningMyFirstApp
---

The process of application design is a daunting one; we've all been there, and you are probably not going to escape making some real blunders in your early designs. The more planning and specific design decisions (even bad ones!) you commit to *before actually starting to code* the more you will learn-by-doing. We are here with advice to help you fix those mistakes once you get stuck on them. The following is a representative example of the process:

----

I'm just beginning to plan the first app I've ever tried to write without following a tutorial, looking for some advice based on others' experience.  I know it's much easier to do it right the first time than to fix mistakes that emerge from bad planning.

My (document-based) application will manage different files associated with songs for musicians that play on a regular basis, who need to build set lists from a library on a weekly basis. 

The primary object will be a song object.  That object will track basic information for a song, such as the title, artist, copyright info, etc... In addition it will need to store a collection of song arrangement objects.  Each song arrangement will hold information specific to a particular variation of the song. There may be a piano version, an acoustic guitar version, and a full band version.  There may be a long version and a short version.  There may be a complex version with a bridge and intros & outros and there may be a simple version with only verses & choruses.  Each of these versions can have multiple documents associated with it.  One or more sheet music documents of some type (PDF, Pages Doc, MS Word, whatever...). A keynote or Power Point file, a file with just the lyrics, etc...

Also, there may be multiple audio files attached to each top level song object.  I would like to give the option of either importing an audio file, or linking to a song ID in iTunes.

With good planning I can work out (though tips based on experience would be appreciated).  What is the best/most efficient way to store the data and the files?  iTunes controls a whole folder-based library to store the audio files, with a data file to track the inventory of iTunes music.   Should I make a folder for each song, then subfolders for each arrangement & then place associated files inside each arrangement folder?  That seems like it would work well in the case of a single computer type of use, but if one user wants to send a music library to another user, it would be hard.

The only other thought I had was to make each document into a package and import the files into the package itself and store them there.  That makes managing the files much easier since they're all wrapped up and you can save/backup libraries no problem, but then you HAVE to use my app to open the documents, or use the "Show Package Contents" option.

A hybrid approach might be to use the iTunes "set library folder" approach, but provide an import/export file format to package up a group of songs to send to another user.

I don't want this to get too long (of course, there's a lot more to this than I've written here), but if anyone feels like discussing some of the pitfalls or suggestions you might have with this kind of app I'd really love to hear points of view from people that are more experienced than I am.  I hope the description of what I'm trying to accomplish isn't too hazy.

Thanks for taking the time to read and respond. :-)

CliffPruitt

----

Try to have your classes only do what they do should. For example, don't have your main controller class do everything. If you have some iTunes code or AppleScript, put that into some type of "iTunesManager" class or whatever. Just try to stay clean and organized with your code. If this is your first app, you'll most likely do it the way you know, and then later as you learn you'll find ways to do things better and fast and more efficient. This is normal in programming, you have to create programs to learn how to program. Add as many features as you can to the app, as this will help you learn how to program better. You will end up researching how to do things many times in the process. Oh, and remember to make the UI nice - get some nice icons for your toolbars and buttons. And last, but not least, enjoy it!

*Sticking to the MVC (Model-View-Controller) paradigm should help keep your code neat. It also makes it much easier to add or change stuff later on. Sorry I can't really help you more, but I've got my own peculiar style of programming and I don't want to inflict it on other people. Good luck!* --OwenYamauchi

----

What is the best way to keep track of a set of files?  Archived Obj, XML FIle, Read From File System Directories?

Basically I need to keep track of files in a way quite similar to iTunes. I'm very new to Cocoa, so I need to balance the best way with finding the easiest development path.

I need to manage a group of external files of various document types such as Pages, Word, PDF, Powerpoint, etc...

I see that iTunes uses a library document and what appears to be a supporting XML document.  Unlike iTunes, I may be creating documents that are packages and will hold copies of all the files inside of it (like iMovie and Garage Band both do with media assets).

Would it be easier/more efficient to simply store each asset as an object and give each object a property that indicates the path inside the bundle to the correct file?

One caveat is that although this most likely will not be an early-implemented feature, I plan to add in the ability to link documents together and want to plan ahead for this. So you might have a document called "worshipSongs.???" that holds a list of commonly used songs.  Then you might have a saved document called "christmasSongs.???" that only has christmas songs. Now if you want to use songs from both sets then, rather than duplicating files and using more disk space, I'm wanting to let the user create a new document which might be "allSongs.???" and open a "Linked Documents" table from which they could browse to both of the other documents. In addition to loading any files that are owned directly by the "allSongs.???" document, the application would also look for libraries in the linked documents & load all of those assets as well.  Would this change the preferred way of saving the data to disk?

----

*I hate to say it, but at some point you are going to have to make your own design decisions and live with the consequences...*

----

I'm looking for insight right now to see if I can do what I want with cocoa.

I have a TV show that runs only in hotels. When tourists get in their rooms, they turn on a TV and it plays on a channel. I am going into a new area with 88 hotels and I am buying a computer-based playback system for each hotel that can be connected to the internet and updated and controlled from any where else on the internet.

MY PROBLEM:  Right now there are 2 way to talk to the units. 1. Take my Laptop to the hotel and connect to it using ethernet. Use the FTP program Fetch to transfer the files, then open up Virtual PC so I can open a windows program called Symphony to create a playlist to send to the unit so it knows what MPEG videos to play in a loop. Way number 2 is to buy a Program that is already created for the macbut the company wants $325 per unit x 88 hotels thats almost $30,000 I don't need all the functions of that program and the fact that I can use a $25 shareware program that does most of the work makes me really want to build my own then spend $30,000.

MY GOAL: I'm not all that new to code. Last year I built a touch screen kiosk using Macromedia Director and lots of Lingo that I learned. I want to build an app so I can sit at my desk and connect to the units in the hotel over the internet and transfer the files and playlists to the units. It would be nice to create an automated system so when it finishes with 1 hotel it starts on hotel 2 with out my need to be there. Can I build such an App using Cocoa? I am about 6 months away from starting the hotel instalations so I have a little time to learn.

Can I build an app that has a built in FTP and create playlists (text files) and perhaps have a terminal to control the units in the hotles?

Thanks for for your insight.

-Lars Wikstrom

----

From your description, it sounds like most of the work won't be handled by the Cocoa API, but rather by third party libraries or Obj-C code you write yourself. Cocoa doesn't really have FTP support, but there are external libraries you can use in your application-- Check out OneButton FTP ( http://onebutton.org/ ) for an open source FTP application that does just that. Creating playlists should be the one thing that Cocoa makes easy; all the methods needed to read filenames, parse them into the correct format and writing it to disk should work well. You could probably use some of the QT classes to give your application a way to preview movies, as well.

For controlling the units-- well, that depends how your hotel software operates. Using sockets, telnet, or whatever is certainly possible, but if you've never dealt with stuff like this in a c based language, it has the potential to be very difficult. Even with 6 months, you might find yourself tight on time if you're learning this while you go.

-DF

----

Hey DF thanks for your response. I bought a book �The Mac Xcode 2 Book� so I can become familiar with the terminology and get going with some basic tutorial programming. I agree, it will be a challenge and your right 6 months might not be enough before my launch but I am willing to try.

The one thing that impresses me is all the open source available out there to aid the design. If I understand it correctly, the FTP link you included is an open source that I can use and link to so I don�t need to write endless lines of code, or copy that code for use in my app. I think the easiest part will be the FTP and list creation. The terminal will be more complicated I have done more research and I found that a program on the PC called �Windows Hyper Terminal� can be used to talk to the units and send it commands play, stop, load and so on.

Is there a similar program on the Mac side? I know that Osx comes with a Terminal.  I�m guessing that perhaps this terminal can communicate with Edje units if it is somewhat like the Windows Hyper Terminal. If so there are thousands of command-lines available to use from the book that I am reading to be used in Coca programming.

I hope I got all that right. Is the Mac terminal similar to the Windows Hyper Terminal ???

Thanks,

-Lars

----

The two are not the same-- terminal.app is OSX's interface to the Unix command line interface on the local machine, similar to running cmd.exe on WindowsXP. Hyper Terminal is a bit different, it's purpose is to connect to modems and hardware, as well as other computers over a network through telnet. It sounds like you'll need to have a good understanding of these subjects, so start researching the way operating systems like Unix work, as well as the network protocols they use. These are not easy subjects to learn-- you should be able to decide pretty quickly if the scope of this is going to go beyond what you want to do in 6 months.

As far as creating an interface in code, that's probably something you'll need to research yourself, once you know the basics of what you're dealing with. It may be possible to find an open source terminal library you can use, or you may have to do much of it yourself using sockets. Remember, data communication is one of the most complex subjects in programming, and learning enough to make it work will be a difficult task for anyone.

Some advice to get you started with the project: start by prototyping on paper and using an interface builder mock-up, and decide exactly what you want the application to do, and how it should do it. Learn object oriented programming concepts if you're not familiar with them, and start thinking about how you want to model data and the design of the application. Then, begin programming a working prototype. Since you're learning much of this as you go, I would create a project for the main application, as well as projects for the major components of the application, that will allow you to experiment and test stuff like connecting to an ftp server, playing a movie, etc, without creating too much of a mess elsewhere. After you've tied these projects together, and the application works the way you want it to aside from the small details (like undo support, for instance), throw everything out and start over; this will allow you to use design concepts and coding practices you've learned along the way to create a program that will have much less bugs and be a lot easier to maintain.

-DF

