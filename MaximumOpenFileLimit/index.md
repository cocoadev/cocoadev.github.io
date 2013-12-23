---
layout: page
title: MaximumOpenFileLimit
---

One problem that often nails me is when I have to open many (hundreds, sometimes thousands of) files in succession is that of the Open File Limit. If you have, for example, a tight loop with an NSDirectoryEnumerator and you're opening an NSFileHandle for each file, then make sure you do a     closeFile! Because the problem is this: the maximum open file limit appears to be ignored when in debug mode. This means your program will act differently when being debugged than it will when it is simply run. This could be something obvious, like a     SIGBUS (usually the case for me because I ignore error detection until one of the last steps of development), or something unnoticeable. Watch out for the open file limit! Does anybody know exactly what the limit is?

--BobInDaShadows

----

It's 10240.     sysctl kern.maxfilesperproc will tell you.

*Starting something from the shell w/o touching limits, it's 256, I'd assume it's similar when started from Finder -- the problem the OP found is, that apparently Xcode gives a higher value when launching products.*

----
Actually, I think the lesson here is to clean up resources you are no longer using. This is more of a resource leak than anything else.

----
Yes, but it also presents a different situation to debug than other instances when you don't clean up your resources. Getting a SIGBUS only when you run the program, and not when you debug, can be kind of confusing and deserves pointing out.

----
This code will set the limit to 20000 open files (call it at runtime):

    
struct rlimit		lim;

// Set the limit
lim.rlim_cur = (rlim_t) 20000;
lim.rlim_max = (rlim_t) 20000;

err = setrlimit(RLIMIT_NOFILE, &lim);


See "man setrlimit" for more details. 

----

Rather than use a random number like 20000, you should use RLIM_INFINITY to say "I want to be able to open as many files as the system will let me".  Folks looking at the code a year from now don't have to figure out what 20,000 really means.

