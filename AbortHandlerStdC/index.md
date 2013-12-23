---
layout: page
title: AbortHandlerStdC
---

The physics library I'm using for my simulation system has a tendency to become numerically unstable when peculiar things happen in a simulation. The library, when things get too hairy, calls abort() to close up shop. Now, my app uses this library and that means that if a simulation goes gonzo the whole app comes down with the appearance of a crash -- e.g. it brings up the crash dialog with the SIGABRT message. Of course, I could do a search-n-replace in the physics library's code to replace abort calls with something more flexible, but I'd rather keep the physics library as is, since I don't have CVS write access to the repository and I think it'd be unlikely the maintainers would accept such a large patch for something which is avoidable through setting up of signal handlers.

So, what I'd like is to be able to catch the abort with a handler, and in my handler stifle it and gracefully stop the simulation and put up an error/warning dialog. 

Last night I read up on the man pages for sigaction and sigblock and friends, as well as my trusty old _Linux Application Programming_ book which, while technically not for Mac OS X, does share quite a bit of largely compatible POSIX & standard C documentation.

In my experiments ( with a dummy program, not my real program ) my handler *was* called when an abort came down from heaven, but the abort *still* halts the program. I tried blocking it in the handler, but it didn't do anything.

Does anybody have any experience with doing this? I know, this isn't a Cocoa question, but since Cocoa is on top of standard C and POSIX we do all have to sometimes play dirty with the stuff.

Here's my code:

    
 #include <iostream>
 #include <signal.h>
 
 using namespace std;
 
 void abortHandler( int )
 {
 	cout << "abortHandler" << endl;
 
 }
 
 int main (int argc, char * const argv[]) {
 
 	struct sigaction act;
 	act.sa_handler = abortHandler;
 	act.sa_flags = 0;
 	act.sa_mask = SIGABRT;
 	
 	if ( sigaction( SIGABRT, &act, NULL ) < 0 )
 	{
 		cout << "Error on sigaction: " << strerror( errno ) << endl;
 	}
 
 
 	cout << "About to abort" << endl;
 	abort();
 	cout << "After about" << endl;
 
 	return 0;
 }


What I get on running it is:

    
 About to abort
 abortHandler
 
 AbortTrap has exited due to signal 6 (SIGABRT).


--ShamylZakariya

----
longjmp() is safe to call from a signal handler.  You can use setjmp() before your call into the library, and then in your SIGABRT handler, do a longjmp.  One thing to be careful of is if the library has allocated resources (they'll probalby leak), and if the library has left things in an indeterminant state.  This could cause you pain next time you use the library.  If the calculations are heavyweight and not interactive, you could put your library usage into another process.  If it goes south and aborts, no problem, just fork a new one.

----
Reading the manpage for longjmp it sounds pretty dangerous for a GUI application; though I don't really know. Frankly, it sounds just a little hairy to me to save/restore the state by rewinding. Perhaps I'm just screwed.

It does occur to me, though, that maybe in my aborthandler I could open a modal dialog which would notify the user that the physics engine has dropped some acid and the program has to be shut down. I could then try to gracefully exit() before the abort causes a non-zero exit code. Basically, anything to prevent the crash reporter from opening, right?

----

It's ok for GUI apps if it's controlled. For example, Cocoa's exceptions system (both the old NSException stuff and the new @try/@throw stuff) are built using setjmp and longjmp.

Given that, the solution sounds pretty easy, depending on how the physics stuff gets called. If you're calling it from Cocoa code, wrap every call in a @try block. Then your abort handler can just throw an exception, which you can catch and handle in a sane manner. The warnings about inconsistent state and leaks still apply, but you can at the very least safely throw up an alert and exit nicely.

----

Now, that's better. I like the idea of using exceptions since they're sanctioned ( even if they're using the setjmp and longjmp ) I feel much more comfortable. Thank you, thank you!

P.S. I never knew that @try and @throw and whatnot were supported in 10.3 until I noticed it in somebody's sample code. Last I saw, you had to do exceptions using NS_DURING and such, which looked pretty hackish ( and didn't semantically grok well ) to me.

Where can I find some documentation on the new exception system? I consider myself reasonably well-read when it comes to apple's documentation, but somehow I completely overlooked this, and frankly, this is *useful*.

--ShamylZakariya

----

It is badly documented. It's only documented in the gcc 3.3 release notes, as far as I can tell. (In Xcode's docs window, click anything under Library in the left panel, then find "Release Notes" in the right panel, then click Tools, then GCC 3.3.) There isn't really that much to cover. If you're familiar with Java's exception handling, they're pretty much the same. @try does what you'd think. @catch as well, and you can have multiple @catch blocks for different classes, and it'll execute the one that corresponds to the class of the object that was thrown. @finally encapsulates code that runs before the other stuff exits, whether an exception was thrown or rethrown, or whether a return statement executed, or anything else. You can @throw any kind of ObjC object.

There's also a (nearly) completely unrelated @synchronize keyword that was added at the same time and is activated with the same compiler switch; it also takes any kind of ObjC object.

These features are *not* on by default; you have to add     -fobjc-exceptions to the OTHER_CFLAGS of your project to turn them on.

--MikeAsh

----

Alternatively, setting your "Mac OS X Deployment Target" to "Mac OS X 10.3" (in Xcode) enables exceptions automatically, without setting any flags.

--RichardNewman

----

Wow -- this trick is pretty cool! 

I wrote some B+Tree code once that used abort() for certain kinds of errors. Basically if the B+Tree's data integrity got mangled it would call abort() to prevent further data corruption. This was a pain the butt for high-level app code which wanted to display some kind of error message. Who knew simply throwing an Objective-C exception would allow apps to circumvent this behavior. Of course, in my case, any subsequent calls into the B+Tree library would likely generate the same error ... 

-- MikeTrent

