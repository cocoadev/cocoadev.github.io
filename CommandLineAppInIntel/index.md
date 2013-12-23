---
layout: page
title: CommandLineAppInIntel
---

I've been having troubles trying to run command line apps built in XCode. I keep getting     -bash: HelloTest: command not found.

I created a new project (Command Line Utility -> "Standard Tool") in Xcode on my intel iMac. It's your generic Hello World command line. I build it with no problems. In my terminal window I navigate to the build directory and I get the above error. Do I have to add the path to my bash profile? If so, how do other programs/developers get around this when they deploy command line utilities? Do we have to tell everyone to add a PATH to their bash profile in order for them just to run a command line utility?

Thanks,
Dan
 
----

The current directory is left out of PATH by default for security reasons. Execute your program by typing     ./HelloTest.

----

Thanks for the reply! That makes sense But it's now coming back     -bash ./RealApp.app: is a directory  ...  ideas please? :)

----

This is because applications on Mac OS X are directories containing a particular structure.  The Mac OS X System Overview or any of the introductory materials on the Apple Developer Connection web site http://developer.apple.com should explain what's going on.

----

Thanks for the pointers. I do know that applications are directories but I was able to execute this app in the command line on my G5 (ppc) but with the new iMac (intel) it comes back with the error message above. I'm having trouble understanding why the terminal behaves differently between a PPC and intel.

----
How about `>open ./RealApp.app` ?
Also, why is a Hello Work command line application packaged as a .app ?  Normally, you just get a plain Unix/MachO stand alone executable from a command line project.

