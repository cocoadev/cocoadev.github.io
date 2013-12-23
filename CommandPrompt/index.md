---
layout: page
title: CommandPrompt
---


I'm looking for a way to launch a terminal windows or command prompt window and pass a command directly to it from a cocoa GUI.  More specifically I'm looking to create a putty type applet for some of my users that sends the command "telnet <some IP address>", to a window that behaves like a terminal window.  The terminal windows should then stay open and allow the user to continue working on the telnet session.  I thought this would be a relatively easy applet to code, but I cant find any info on it. 

If someone has any suggestions or if there's already some documented method of doing this I would appreciate if someone can point me in the right direction.

----
I believe you can use Terminal's AppleScript dictionary to do this. Another alternative would be to generate a shell script, then open it with Terminal using NSWorkspace.

----
You have no idea the world of hurt you'd be putting yourself through if you tried to implement this on your own.  Writing a terminal emulator is NOT fun (esp. when dealing with Macs, which send different control codes for delete and such).  If they're merely going to be using a Telnet session, why not just have them use Terminal.app?

----

Check out this link...

http://iterm.sourceforge.net 

--zootbobbalu

----
You could also just perform the system() function:

    
#include <stdlib.h>

int main(void)
{
    return system("cat x"); /* The system() function returns the error code */
}


If you need speed, then use the Mac OS X core functions fork()/exec():

    
#include <unistd.h>

int main(void)
{
    exec("cat x");
    write(2, "failed run\n", 10); /* exec just returns if it fails, otherwise the current program quits */
    return -1;
}


In a Cocoa program:

    
#import <unistd.h>

// ...

if (fork() == 0) { /* splits process into two, returns 0 if the child process */
    exec("cat x");
    NSLog(@"failed execution");
}


All you need to do is figure out how to parse the output into a NSTextView if you intend to. - PietroGagliardi

----

None of you realize that he needs the user to be able to use the telnet session.  At which point you should use Terminal.app (or another terminal emulator), and launch it using NSWorkspace.  Launching GUI apps from the UNIX domain -- i.e. fork() + system("open Terminal.app") -- is unsupported (look up Mach bootstrap namespaces) and probably won't work in Leopard.

----
Try encapsulating it in a shell script with Platypus - PietroGagliardi

----
The dumbness of the code examples on this page astounds me. There isn't even a function named     exec, and the     exec* class of functions will not be able to parse a string such as     "cat x".

----

Agreed. This is the lowest-quality grouping of responses I've seen on this wiki in a long time. I will second the opinion above that rewriting a terminal emulator is a **huge** task. If you're not even able to clearly define your goal (which the OP's wording indicates), such a task is way beyond your experience. How about backing up and taking a much broader look at what you're trying to accomplish. Not in technical terms, but in "user-wordable" terms - terms you'd use to describe the feature to potential users. That way, you free yourself from the details (in which the devil resides, I'm told), and allows others to suggest approaches that aren't quite so out of your league.

----
And to responders, particularly PietroGagliardi, it helps nobody to jump in with a bunch of responses that not only probably aren't what the original poster was looking for, but are just plain nonsensical and wrong in basically every context. If there is some question about what the poster wants, ask him, don't just pick something and run with it. If you don't know enough about a particular API or system to give good examples, just give general directions. There's no shame in limiting yourself to "check out     fork/    exec".

These sorts of broken responses are of little value and this page is basically useless. I don't want to be the Quality Police, and I don't think we should need any, but always write your responses with the idea of increasing the overall quality of the wiki.

----
Just to say something: my first response involved Unix system calls (MacOSX is based on Unix), and my second option involves a program that turns shell scripts into executables. This is an obviously difficult topic, and I think that the most direct approach is to have the people run the Terminal. - PietroGagliardi

----

... except that "    exec("cat x");" isn't part of the C libraries, and I believe this is what most people are talking about -- the obvious inaccuracies and lack of error checking when giving advice. It only serves to confuse people and dilute the wiki.

----

Oh come on... this wiki isn't a reference manual.  So what if he said     exec("cat x") instead of     execlp("cat", "x", NULL)?  The tiny little inaccuracies like that are really not that important, especially in the face of the more fundamental problems of what approach to take.  Mistaking the parameters for the     exec functions is irrelevant; the fact that people are recommending the use of such hackish solutions without regard to the approach at large is what scares me.  The "kitchen sink" method of programming is never a good thing.

----
Odd that exec() was removed from the SUS. See http://opengroup.org/onlinepubs/009695399/functions/exec.html. It was very strange because the exec() call was one of the original 7 calls that formed the basis of Unix's uniqueness (fork, exec, open, creat, close, read, write)

Anyway, we have to close this discussion because it obviously got out of hand (sorry). - PietroGagliardi

----

Nobody said it was a manual but it **is** a reference tool and it was more than a mistaken parameter ... it was a completely nonexistent function posted as a code example. Providing broken code examples is not okay and every instance of such a mistake is corrected when it's found, but rarely does someone post a code example as an authoritative answer with such blatant lack of fact-checking. When they do, they're told to check their facts. If you don't like being corrected, either make some minimal effort to check your facts before you post or don't post, because putting inaccurate information on a wiki is going to get you corrected. Putting blatantly incorrect information on a wiki is going to get you chided.

----
IMO the fact that somebody recommended     exec("cat x"); is very bad. First of all, the function doesn't exist. Second, the function that does exist does not parse shell command strings. The entire idea is mistaken and makes no sense. Either the person posting it is so inexperienced that he doesn't realize how very little he actually knows about the subject, in which case he should limit himself to reading other people's advice for a year or so, or he does realize it and decides to post anyway, in which case he's just a fool and should limit himself to reading other people's advice indefinitely until this changes.

Given the followups, the second choice would appear to be correct. Pietro is young and no doubt will become a fine programmer one day. But until that day arrives, he should be more careful. The need to help people can be strong, but attention should always be paid to ensure that this need doesn't overshadow the actual helping. ShotgunDebugging is even worse when applied to other people's problems. In a case like this, I have to say that bad advice is worse than none. -- MikeAsh

----

Cocoatech has a subclass of NSView, CocoaTechTerminal, which I think is much along the lines of what you're looking for. It's available as open source, at http://www.cocoatech.com/opensource.php, and is used in PathFinder. Much easier than writing your own.  --DavidCooke

----

FWIW I got this working through:

    
#!/bin/bash

theCommand="cd $1"

# create our applescript which will launch a
# new Terminal window if there is not one running
# and otherwise run the command we want in the
# current running Terminal window
aplscr="
tell application \"Terminal\" to activate
tell application \"Terminal\" to do script \"$theCommand\"
"

# execute the applescript
osascript -e "$aplscr" &>/dev/null &

