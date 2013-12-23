---
layout: page
title: NewbieProjectIdeas
---

The title is pretty self explanatory, but would you guys help people like me (newbies) by creating a list of projects you think would be both simple and yet still complicated enough to broaden our cocoa horizons? Maybe projects you have done and found interesting? Basically a list of projects that will help us ignorant folk become cocoa masters.
--JohnDevor


== Beginner ==

* Web Browser:  It would take two controls.  One where the user would enter the address.  And a second where you would display it. You could just use the simple stuff to render the html (i.e the API TextEdit is using).  Then when you want to get fancy add the ability to click on links, etc...  You would use the [NSString stringWithContentsOfURL] function to get the webpage. -- SaileshAgrawal (with the advent of the WebKit framework this could probably be moved to the Beginner category - John) This can be done without ANY coding. See if you can do it.
* Shape Drawer:  Create a window with a pull down menu that has the name of two or three shapes.  Add a custom view.  Make it so that whichever shape is chosen from the pull down menu, is drawn in the custom view.
* Image Viewer: Create a window with an NSImageView.  Code your program so that when the user chooses "Open" from the "File" menu, an Open panel comes up, and the picture file (jpg, pdf) is put in the NSImageView. Use an NSDocument format so that it is similar to Preview for an added challenge.
* Movie Viewer (similar to Image Viewer above): Create a window with an NSMovieView.  Code the program so that when the user chooses "Open" from the "File" menu, an Open panel comes up, and the movie file is put in the NSMovieView.
* Convert Shape Drawer to a ScreenSaver module that draws random shapes in random places in random colors.
*Create an alarm clock and sleep timer that plays a select mp3, beeps at you and allows you to snooze afterwards. Make sure you give the user correct feedback. Doubly make sure it works so that they wake up (sleep on it to test...thats gotta be a first).
* Make your own DockLauncher (Include user preferences. Make it launch apps, files, and urls).
* Create an application that creates a borderless window using an NSWindow subclass and then set the window's level to kCGDesktopWindowLevel. Then make your app display something cool (preferably animated) in that window and try it out. :-)
* HTML editor with integrated WebKit preview. Create an NSDocument based project, add a NSTextView and a WebView to the document window, and tell the WebView to render the HTML from the text view whenever the text view's contents change. (Hint: delegates, notifications,     - (void)textDidChange:(NSNotification *)aNotification)
*Create a StandardService that does something interesting with selected text. Capitalize it, encrypt it, look it up with Google, pipe it to a unix tool...


== Intermediate ==

* The Mandelbrot Set:  Create a program that when a button is hit, the mandelbrot set is shown in a custom view.
* The Mandelbrot ScreenSaver: You got it: random (Interesting!) locations in the set. Yeah yeah, it's been done before, but not by you!
* The Game of Life:  Create a program that demonstrates the Game of Life (see Martin Gardner's rules here [http://ddi.cs.uni-potsdam.de/HyFISCH/Produzieren/lis_projekt/proj_gamelife/ConwayScientificAmerican.htm])
* The Game of Life ScreenSaver: You got it: display random life simulations in a screen saver. Yeah yeah, it's been done before, but not by you!
* Pong or Breakout:  Write an app that simulates one of the original computer games
* The Pong or Breakout ScreenSa ... uh ... nevermind.
* Write an app that gets a certain Project Builder project directory, moves the build folder, compresses the project directory, backs it up to another part of the computer, replaces the build directory and removes any resulting mess.
* Write an InterfaceBuilder palette that allows you to set up an NSArray or a NSDictionary in a nib file. (Or cheat and get the code from FoundationCollectionsPalette.)
*Write an MP3 player.
*Write a Bonjour-enabled (formerly Rendezvous) MP3 player.
*Write a Bonjour-enabled (formerly Rendezvous) enabled chat client complete with smilies, file transfer and sounds.
*Image Viewer 2: Create an image viewer application that allows browsing of directories, showing thumbnails for image files in a particular directory.


== Advanced ==

* Web Archiver:  Make a program that will go to a website, archive the html to a directory of the user's choice, then archive all the linked websites on the first webpage.
* Plotting App: Put a front-end on the newly released Cocoa plotting framework, Narrative ( http://sourceforge.net/projects/narrative ). Incorporate the open-source cocoa spreadsheet class RRSpreadSheet to form a useful plotting application from freely available parts.
* The Mandelbrot Set part II: Allow the user to open the Mandelbrot set in a document window. Allow the user to click & drag an area of the image to zoom into. Display the current coordinates, level, maximum iterations, etc., in the window, and allow the user to change these if they like.
* The Julia Set part I: Allow the user to open a Julia set in a document window. Allow the user to click & drag an area of the image to zoom into. Display the current coordinates, level, maximum iterations, etc., in the window, and allow the user to change these if they like.
* The Julia Set part II: In a special window, display a Mandelbrot set document window (from The Mandelbrot Set part II). Again, the user can zoom in and the like. When the user double-clicks a point on the Mandelbrot, a Julia set window opens pre-initialized to the coordinates indicated in the Mandelbrot set. (Recall that the Mandelbrot set is essentially a road-map of Julia sets, and while there are an infinite number of Julia sets, there is but one Mandelbrot set, and in the darkness bind them.)
* The Mandelbrot Set part III. Allow the user to open a Mandelbrot document window (from The Mandelbrot Set part II). Again, the user can zoom in and the like. Allow the user to "Keyframe" specific points on their journey into the Mandelbrot set (I.e., "zoom to this place", "set keyframe", "zoom even deeper", "set keyframe", etc.). there should be no more than one keyframe at a given zoom level. Allow the user to select a menu item that renders their descent into the Mandelbrot set to a Quicktime movie. Display the QuickTime movie in a movie document window (from Movie Viewer). Try smoothing the animation trajectory into a curve, instead of a series of straight lines.
* The Julia Set part III. Allow the user to open a special Mandelbrot document window (from The Julia Set part II). Allow the user to plot "keyframes" on the Mandelbrot window. Unlike in The Mandelbrot Set part III, allow the user to plot multiple keyframes on a given zoom level, and connect the frames w/ a line (or, later, a curve). You may need to disable zooming to limit the scope of the problem. Allow the user to select a menu command to render the user's path through the Mandelbrot set as a series of Julia sets. Display the QuickTime movie in a movie document window (from Movie Viewer). Allow saving/opening the QT movie and the Mandelbrot data set.
*Home Directory Cleaner: Enumerate over a user's home directory (or disk drive, or whatever) and display the size of each subdirectory, perhaps using a NSBrowser. Also display other file stats, such as last mod date. Allow the user to delete (Move to Trash!) files and directories from your program. Allow the user to compress files and subdirectories as well. Store these commands, along with some navigation controls, in a toolbar. (Note, in Panther, the "ditto" command will allow you to archive directories into CPIO files, complete w/ resource forks. In Jaguar and earlier, you'll need to find a 3rd party archive solution. Or write your own compressed archive format. MikeTrent can answer questions on this topic). Have the program recommend removing/archiving specific files based on the file age, name, and location. 
*Command Line Worksheet: Allow the user to open a text document. When the user presses the "Enter" key (or Cmd-Return) execute the selected line(s) as if they were a shell command. Collect output from the commands and insert them into the document. Add syntax hilighting, tab/space shifting (cmd ]), auto-commenting (a command to add/remove comments), and other programmer power features.
*Command Line Worksheet part II: Start with Command Line Worksheet. Add a class to Command Line Worksheet that adds/removes custom command menus to Command Line Worksheet; instantiate the class as a singleton object. Have the singleton object register for a DO port (see NSConnection). The menu manager object should be able to add new menus to the app, add new items to menus, add sub-menus, etc. It should also be able to remove any menu or item it created (but not other menu items!). Menu commands should invoke one or more lines of shell-script instructions as if they were typed in the worksheet; output should display in the worksheet normally. Write a command-line tool, AddMenu, that connects to your worksheet's menu manager object via DO and issue commands to add menus and menu items. Write a similar tool, RemoveMenu, that connects to the menu manager and removes menus and menu items. Put these items in the application's Resources directory, and allow the user to run them easily from the worksheet menu. (Note: this is easier than it sounds -- maybe a few hours of work once you get the basic Command Line Worksheet up and running.) -- MikeTrent
*"Extensions" palette: Make a palette that using interface builder settings ONLY (No need for connections or anything) can load code from a bundle for a widget. What I mean is, something that would allow you to add to already-compiled-applications. Just copy the app, open up its directories, slap in a few bundles, and edit a nib. I think the palette would have to have an item that you drop on the document window instead of adding to an application window, and buttons/other stuff could make a connection to it to perform an action. The only "challenge" here is setting up the palette and figuring out how to get the dynamic loading to work. I'm doing something like this myself. 


If you're a newbie, try this link for basic tips on HowToProgramInOSX.  Please feel free to change and add to the explanations to make them more clear and complete.

If you figure out how to do any of these projects, please post solutions here... NewbieProjectSolutions (warning spoilers await you!)
----
This is great, thanks a lot. If anyone wants to keep adding to this list that would be swell. :)
-- JohnDevor
No problem. -- AlexanderD

----

*
Write an app that gets a certain Project Builder project directory, moves the build folder, compresses the project directory, backs it up to another part of the computer, replaces the build directory and removes any resulting mess.
*

I am using the following script to do a tar + gzip of all files in the current directory (recursively) except the build directory and the IB backup files (<name>~.nib). The resulting archive is named after the current date and time (YY.MM.DD to get proper sorting).

    
 #!/bin/sh
 
 find . \! \( -regex '.*/build/.*'      \ 
          -or -regex '.*\.tgz'          \ 
          -or -regex '.*/.*~.nib/.*'    \ 
          -or -regex '.*/.*~.nib' \)  >/tmp/files
 
 tar -zcf `date +"%Y.%m.%d_%H_%M"`_backup.tgz --no-recursion --files-from /tmp/files


--AllanOdgaard

Hmm... that completely fails to work for me at the moment, and I'm not enough of a UNIX-head to know why or how to fix it. Still, I like the idea of automating it...

-- RobRix

*You need to put the 6 lines in a script (e.g. backup.sh) and set the x bit (    chmod u+x <file>), then you can execute the script (    ./backup.sh) and it will create a compressed tar file with current date as prefix.*

*Hope it helps...*

----

I added a bunch of fractal variants -- all of which I ended up writing in 1994 on my Centris 650. I remember it as if it were yesterday -- writing a few thousand lines of Pascal to do my Julia Set animation. (for you CornFedRaverNewbies out there, Pascal is a programming language. No, it's not at all like Python. **Sure it is. They both start with 'P', for instance.**) Later I would get a PPC upgrade card and switch everything to C. Sigh ... that was back before I discovered the coding power of beer. ... Anyhow, this is a lot easier now with Cocoa. Go for it. -- MikeTrent

----

I've always found that one of the hardest steps of programming is releasing your product to the wild for the first time. In that regard doing some of the above projects once, then redoing them again to fix mistakes, make it more efficient and apply new ideas you have learned works wonders. 

----

Anyone have an example of rendezvous implementation?

----

Mathematica 5 has been available for a little while now, so most people are familiar with the newer features as well as Wolfram's obvious use of it within his recent book, for simulations and the whole "small-simple-programs-to-elaborate-behavior-programs."  I use Mathematica quite a bit more than I thought I would, it's just a lot faster than doing stuff by hand, and the visuals are decent.  So, here's my newbie project idea (which might be overkill as a newbie project) for people who can't afford, or understand how to use, Mathematica---- knock-off simulators akin to the simulators used in Wolfram's book that demonstrate how simple programs can adapt/evolve/create/etc. to become elaborate programs.  Admittedly, there are plenty of Tesselation Simulators, plenty of "Game of Life" simulators out there, but what about "Game of Life" type simulators bred with "Genetic Algorithm" simulators.  Now, that would be interesting, using genetic algorithms to create a more realistic Game of Life application.  I think there are a few that exist, and I admit, I started working on something similar a few weeks ago, but maybe someone with more imagination should take the idea.  I know I'm not necessarily proposing anything new, but I just thought I might add to the discussion. (Sorry if this is overkill for this particular page) --TriLateral

----

