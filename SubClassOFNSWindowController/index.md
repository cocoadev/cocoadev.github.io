---
layout: page
title: SubClassOFNSWindowController
---


I am designing a document-based application that sub-classes General/NSDocument and General/NSWindowController called General/ViewController, but i am having a problem.
When I make a new General/NSDocument it runs through the initializing messages just fine and it goes though makeWindowControllers just fine,
but after everything is said and done.  It seems that the General/NSWindowController outlets do no seem to work at all, they are all nil.

    
In the makeWindowControllers i have
makeWindowControllers { 
	General/NSLog(@"I am in makeWindowControllers"); 
	windowController = General/[[ViewController alloc] initWithWindowNibName:@"General/MovieController"]; 
	[self addWindowController:windowController]; 
	if(movieFile != nil) { 
		General/NSLog(@"Setting movieView to movie"); 
		[windowController setMovieWithFile:movieFile]; 
	} 

	else 
		General/NSLog(@"There is no movie variable yet");
}


The problem i am coming across is in the code of setmovieWithFile.  which is
    
-(void) setMovieWithFile:(General/NSString *)file {
	if(movieView == nil)
		General/NSLog(@"I am nil");
	General/NSLog(@"set movie using file");
	movie = General/[[QTMovie alloc] initWithFile:file error:nil];
	General/NSLog([movie attributeForKey:@"General/QTmovieFileNameAttribute"]);
	[movieView setMovie:movie];
}


it ends up saying movieView is nil, and obvious nothing else will work.
So when does the connections from IB connect during the code.  And when should i be able to use movieView, which is
connected to a General/QTMovieView in IB.

Sorry if this is a pretty stupid question, i am pretty new to cocoa and i am having trouble understanding why this isn't working.
Thanx for the help.
~
Stargex

----

Did you override     initWithWindowNibName: in V<nowiki/>iewController? Did you remember to call     super? It's the little stuff like this that always screws up my projects. --General/JediKnil


----
no i did not overload initWIthWindowNibName, should I have?  My General/QTMoviewView is getting its General/QTMovie file from an General/NSString that is in a Controller file in the General/MainMenu.nib....   So i didn't see why i should overload that method. 
~
Stargex

----

General/NSWindowController loads its nib lazily. You aren't doing anything in your code to force the nib to load, so your outlets are all nil. Try inserting     [self window] at the beginning of     setMovieWithFile: to force the nib to load.

----

Thanx a bunch that self window message worked.  I was wondering what was wrong.
~
Stargex
