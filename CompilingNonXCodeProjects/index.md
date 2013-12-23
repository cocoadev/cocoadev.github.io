---
layout: page
title: CompilingNonXCodeProjects
---



Hey, guys, This is a total newbie question but I'm trying to get a binary of the LAME MP3 encoder on my system.  Source Forge just offers the source (as far as I can tell, but the site kind of confuses me). I managed to do it somehow on another system, but I had a pretty decent tutorial in front of me at the time.  I have an AppleScriptStudio project that uses the Lame binary (via the DoShellScript command).  I believe the AppleScript part should compile on this new system fine but I dont have the Lame binary anymore.

Can you guys point me to a page somwehere or maybe a section in the documentation that explains compiling non X Code stuff?  Either using GCC through the command line or importing the source into an Xcode project?

One caviat is that I'm not really terminal savy so I'm looking for a way to do it that isn't going to risk breaking my system. :-)

Tiger / Xcode 2 if it makes a diference.

Thanks,
CliffPruitt

----

Usually there is a README or INSTALL that you can glance at and it should give you basic instructions. What you'll probably do is something like:
    cd pathToDirectory
./configure
make
make install

----

Thanks for the tip.  I tried it and it worked... sorta.

I know this is a little outside of the realm of Cocoa, but its got to do with compiling so hopefully you guys might cut me a little slack this time. It seems to have compiled, and then when I tried to install the first time it gave errors about permissions.  So I did used 'sudo make install' instead of 'make install'.  I can 'cd' to /usr/local/bin and see the lame executable, but when I just use the lame command it tells me that the command does not exist, even when I'm in that directory.  I also can't find a man page, which I believe there was one (coudl be wrong).

Did I do something obviously wrong here? I've saved the terminal compile / install output if that helps explain anything.

----

*you probably just  need to add /usr/local/bin to your $PATH environment variable in ~/.bash_profile or wherever*

Maks sure to chmod it +x.

----

I'll try adding the path.  Am I wrong however, in understanding that if you're in the directory containing the executable & you type the executable name, that it should be run?  I'd tried running with "sudo" in case permissions are wrong but that doesn't work either.  I'm just wondering if I needed to do something different with the compile.

----
Yeah, unfortunately you are wrong. To run a program in the current folder, type "    ./*executable*" (without the quotes). Also,     /usr/local/bin should already be in your     $PATH, but it's a good thing to add if it isn't. --JediKnil

*    /usr/local/bin doesn't even exist on a default install of OSX, so it's not in the default path.*

----

OK making some headway.  I can run it with "./lame" but what is preventing the lame command from being accessed without referencing the executable itself?  Was that a compiling error or my $PATH variable or what?  Any way to fix it?  I dont mind recompiling, but its not going to do much good if I dont know what wasn't right the first time around.

Thanks a lot for your help.  I know this is a little off topic for this site.

----

If it executes at all, it's not a compile error. In Terminal, type     echo $PATH Mine looks like

    /bin:/usr/bin:/usr/games:/usr/X11R6/bin:/usr/local/bin:/opt/local/bin:/sw/bin:/Developer/Tools:/usr/sbin:/sbin

if one of your entries isn't /usr/local/bin you need to add it by editing (or creating) ~/.bash_profile and adding something like

    
PATH=:/bin:/usr/bin:/usr/games:/usr/X11R6/bin:/usr/local/bin:/opt/local/bin:/sw/bin:/Developer/Tools:/usr
/sbin:/sbin
export PATH


Then type     source ~/.bash_profile and it should work without being in /usr/local/bin.

----

$PATH solved the problem.  Thanks.

For my future reference let me see if I understand this:

1. Executables themselves must be references using ./execName
2. Terminal commands are not the same as executable names
3. The $PATH variable is loaded each time a terminal session starts.  When you type a terminal command, it looks in the directories specified in the $PATH variable for an executable of the same name.

so basically is the $PATH variable what is responsable for defining terminal commands?

----

1. No. Executables must be referenced by a location in your $PATH variable, otherwise you have to use ./ You can add **.** (dot) to your path, which means 'the current directory' and then you wouldn't have to prefix with ./, but that's a bad idea, as a malicious package could have a script named     ls that deletes your home folder when  you try to list its directory. If . comes early in your path, you're out of luck. Or it could have a     make script that when run with non-root permissions, called the real make, but when run with root permissions (for the 'sudo make install' bit) deleted your entire hard drive. All in all, bad idea for the little bit of convenience it provides.

2. Yes, they are (for the most part - some commands are built into the shell). The issue is defining where to look for those commands.

3. Correct.

The $PATH variable is responsible for defining the *location* to look for commands entered in the terminal.
----
Gah, MultipleEditSyndrome.

The dot means "current directory" in all Unix-based systems (afaik), just as two dots means "parent directory." So when you type in a command name, the terminal looks in the directories (as you said) for an executable. But for *any other executable,* you need to type in a path.     /bin/bash is a valid command (assuming, of course, that     bash lives in the     bin folder), as is     ./bash if you are in the same folder as the executable. In fact, any path can be considered a valid command (relative or absolute) provided that an executable lives at the location referenced. However, a name on its own will be looked for in each of the     $PATH directories. To look for commands in the current directory, just add a colon and a dot to the end of your "    PATH=" line (though as the above poster said, it's not necessarily a great idea for security).

Sorry about the     /usr/local/bin thing...the anonymous poster above was right. --JediKnil

----

All good information, thanks.

I guess I was thinking in terms of when you use the "ls" command it just assumes that you mean "current directory" as an argument whereas typing an executable name seems to require a path, even if the path is "./".  But I do see the difference between an argument passed into a command and calling a command itself so I suppose it all makes sense.

Thanks again, you guys have all been quite helpful.
CliffPruitt

And by the way, my AppleScriptStudio app is up & working on the new system wonderfully.  Thanks!  If I can figure out how to do the same thing with Obj-C I may try & convert it before too long.

