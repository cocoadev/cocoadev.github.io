---
layout: page
title: UsernameAsNSString
---



How can i get the Full Username of the User Logged in in OS X?
I dont neet the short name... i want the Full name... http://goo.gl/General/OeSCu
Anyone has a clue how to do?

Greetings chris

----

How about General/NSFullUserName()?  It always helps to grep through /System/Library/Frameworks/**/*.h...

--General/NicholasRiley
----
hi General/NicholasRiley

Can you explain me what "grep through /System/Library/Frameworks/**/*.h" means? I 
i always looked in the Cocoa Documentation in the Developer folder...

Greetings Chris

PS: General/NSFullUserName was the function i was searching but i havent found it in the Docu :-/ but works... thanks much for help
----
Unfortunately, sometimes the documentation is not 100% complete. It is always useful to search through header files, which really are the true source of documentation for code. The documentation we use just explain most of the aspects of the code so it's easier to understand.

BTW, grep is a Unix text searching command accessible in the command line. I'm not totally familiar with the process by which you use it to search through files, but 'man grep' will certainly tell you.

----

Here's a command-line I find myself using a lot (This is especially good if you have a worksheet-like interface like General/BBEdit (I wrote my own...)). This uses an amazingly obtuse unix command called "find" which recursively descends through directory trees looking for stuff. I've asked the find command to find plain files that end in ".h" that contain the word "username" in it. Change the path to /usr/include to search through the darwin headers as well ... 

    
find /System/Library/Frameworks -type f -name "*.h" -exec grep -i username '{}' \; -print


Of course, Project Builder will search through header files for me ... click on the Find tab above the source code text field, click the options button, search Project Files and Frameworks, close the options panel, type the text you're looking for, and off you go. People uninterested in Terminal might go this route.

I agree, searching the headers for information is often a lot more direct and satisfying than searching the documentation. Documentation, however, usually has overview information that tells you how to use the thing you're looking at ... 

-- General/MikeTrent

----

One of my favorite features in zsh, my shell of choice, is that with its directory-searching wildcards (the technical name is 'globbing') you can dispense with many uses of the find command.  So, to perform the above operation, all I need to do is:

    
% cd /System/Library/Frameworks
% grep -i username **/*.h


** indicates that zsh should match any number of nested directories.  However, in some cases you may get an error message:

    
% grep -i username **/*.h
zsh: argument list too long: grep


So, what's going on?  zsh is expanding **/*.h into a very long list of file paths, and the list is too long for the OS to handle.

    
% sysctl kern.argmax
kern.argmax = 65536
% echo username **/*.h | wc
      1    1876  118929


kern.argmax is the (fixed at kernel compile time) maximum length of an argument list, and from the output of wc you see that there's almost twice as many characters in the list.  There are a couple of alternatives, however.  (Why doesn't echo complain where zsh did?  The echo command is built into zsh, which doesn't have this problem; if you tried to call /bin/echo directly instead, it would give you the same message as grep.)

One thing you can try is a for loop:

    
% for i in **/*.h
for> grep -Hi username "$i"


This invokes grep once per filename, just as the find command does.  It works - the -H flag causes grep to print the matching filename, even if there's only one filename on the command line - as grep is being invoked above.

The cleaner and faster solution involves print, another zsh builtin, with xargs.

    
% print -N **/*.h | xargs -0 grep -Hi username


xargs takes a delimited list of arguments (the default is whitespace-delimited; the -0 means it's null-delimited, which helps because many paths in OS X contain spaces) and feeds it to a command, taking care not to exceed the argument length limit.  The -N on print does the reverse, inserting a null between each argument.

If you want to see what xargs is executing, use 'xargs -0t' and it'll print the commands before it runs them.  In the above example, xargs runs grep twice.  Why still use -H if grep is getting loads of arguments?  I'm paranoid, and xargs could get unlucky and have only one filename left for its last invocation, which would cause grep to omit the matching name.

Hope this helps, and whets your appetite for useful shell tricks :-).

-- General/NicholasRiley
----
Hey...
Thanks much for the additional info... im not very used to the terminal app or unix commands... i only know some basics like "ls" or "cd" or "chown" e.t.c... grep was new for me.. but with this very good explanation i think i can do something :-)

----

here's how I usually use grep to search the headers:

    
grep -RF General/NSFullUserName /System/Library/Frameworks/{Foundation,General/AppKit}.framework/Headers --include=**.h'


grep -R: recursive (descends into directories).

grep -F: fixed string (not a regular expression). this is more efficient than searching by a regular expression if you're not using regexp wild cards, and can also return more accurate results (for example, searching for 'General/NSString.h' returns only matches for 'General/NSString.h' as a fixed string but also includes 'General/NSStringah', 'General/NSString!h', etc. as a regexp). you can also use fgrep instead of grep -F.

{Foundation,General/AppKit}: zsh clones the argument in which this was found, and replaces the {...} with 'Foundation' in the first argument and 'General/AppKit' in the second.

grep --include=**.h': only searches files matching the glob (which must be put in apostrophes or escaped with backslashes or zsh will try to expand the glob itself). you can provide more than one --include= argument. as shown, doing this to the Headers subdirectories of a framework is not very useful because those folders only contain headers anyway, but it can dramatically improve performance (and narrow the scope of the search) when searching in, say, a CVS or Subversion checkout.

using find with -exec on the headers is a good way to go through a lot of process General/IDs and waste a lot of time, since find will begin a new grep process for every file it finds.

*--boredzo*

*Process General/IDs are free, so that's not a big worry. Do you have any evidence about wasting time? I would guess that this kind of operation would be seriously I/O bound and the extra time required to start a new instance of grep every time would not be greatly noticeable.*
