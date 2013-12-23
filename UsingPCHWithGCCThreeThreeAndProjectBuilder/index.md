---
layout: page
title: UsingPCHWithGCCThreeThreeAndProjectBuilder
---

General/JeffHarrell mailto:jharrell@mac.com

I've come up with a method for using General/GCCThreePointThree's PCH feature with General/ProjectBuilder. General/ProjectBuilder knows about GCC 3.1's PFE, but General/GCCThreePointThree doesn't do PFE. If you don't use precompiled headers at all, your compile times will be unacceptably long. So I spent a little time figuring out how to do PCH with General/ProjectBuilder.

(This entry assumes you've already installed the December 2002 GCC Update, and know how to set the various compiler settings in General/ProjectBuilder to use GCC. Refer to the readme that came with the December 2002 GCC Update for instructions on how to get a project building with General/GCCThreePointThree.)

The first thing to know is how to compile a header with General/GCCThreePointThree. To do it, pick any Objective C header file and do this:

    gcc-3.3 -x objective-c-header foo.h

The result will be a file in the current working directory called foo.gch. (That's GCC Compiled Header, I guess.) If foo.h includes other header files, you may need to fiddle with the compiler's search path by using the -I flag on the command line.)

One of the coolest things about General/ProjectBuilder is the prefix header. You can designate a prefix header to be automatically included with any source file in your project. If you create a project using one of General/ProjectBuilder's templates, you get a prefix header automatically. Say your project is called Foobar. There's a file called Foobar_Prefix.h in your project directory. This is the header we want to precompile.

So if you go into your project directory and type

    gcc-3.3 -x objective-c-header Foobar_Prefix.h

you'll end up with Foobar_Prefix.gch. When you build your project with General/ProjectBuilder, the compiler will automatically find Foobar_Prefix.gch, and your project will compile much more quickly.

But manually compiling Foobar_Prefix.h kinda sucks. It would be much better if General/ProjectBuilder could make Foobar_Prefix.gch for us, and keep it up to date. We can do this. We do it with a Makefile.

Here's the Makefile I've created to do this job:

    
ifeq ($(PREFIX_HEADER),)
$(error PREFIX_HEADER must be defined)
endif

ifeq ($(DEBUGGING_SYMBOLS),YES)
DEBUGGING_CFLAGS=-g
else
DEBUGGING_CFLAGS=
endif

$(PREFIX_HEADER).gch : $(PREFIX_HEADER)
	$(CC) $(DEBUGGING_CFLAGS) $(OPTIMIZATION_CFLAGS) \\

$(OTHER_CFLAGS) -I . -x objective-c-header $(OTHER_CFLAGS) \\

$(PREFIX_HEADER)

clean : 
	rm -f $(PREFIX_HEADER).gch
	
install : $(PREFIX_HEADER).gch


Save this file as "Makefile" in your project's directory. Be careful with the lines ending with \. There must be no space between the \ and the return at the end of the line.

This Makefile depends on the variable PREFIX_HEADER being set before its run. We'll get to that in a minute.

When this Makefile is invoked (assuming PREFIX_HEADER is set), it will look to see if PREFIX_HEADER.gch exists. If it doesn't, or if it's out of date relative to PREFIX_HEADER.h, it will compile PREFIX_HEADER.h. That's it. Pretty simple.

What we want is for General/ProjectBuilder to run this Makefile before it builds our project. If PREFIX_HEADER.gch doesn't exist, General/ProjectBuilder will create it. If it does exist but it's out of date, General/ProjectBuilder will rebuild it. If it exists and is up to date, this Makefile will be a no-op, and the project will compile as usual.

We do this by creating a new target in General/ProjectBuilder, a legacy Makefile target. (Project menu, New Target, choose Legacy Makefile, call it "Precompiled Header" or "PCH" or whatever you like.) When you do this, you'll see your new target under the list of targets in General/ProjectBuilder, and you'll get a list of target settings. By default, "Pass build settings in environment" is checked, which is what we want. We're almost ready to go. First, we need to take care of setting PREFIX_HEADER.

Under "Build Settings," click the plus sign to add a new setting. Call the setting PREFIX_HEADER, and set the value to Foobar_Prefix.h or whatever the name of your prefix header is. That's it. We're done.

Set your active target to your new makefile target and do a build. You should see something like this in your build pane:

    
Building external target "Precompiled Header"
Build succeeded


If you drag out the subview, you'll see the actual build commands:

    
/usr/bin/gnumake 
cc -g -O0  -I . -x objective-c-header  Foobar_Prefix.h


Now if you look in your project directory, you'll notice that there's a file, Foobar_Prefix.gch, ready and waiting. If you select your main target again and do a clean build, you'll find that your project compiles much, much faster, because the compiler automatically finds the precompiled prefix header in the project directory.

The last step is to make sure the makefile target is built every time the main target is built. To do this, we make the main target dependent on the makefile target. Simply drag the makefile target onto the main target. The disclosure triangle will rotate and the main target will expand automatically. This creates a dependency.

Now do a clean and a new build. You'll notice that General/ProjectBuilder runs the Makefile first, which compiles the prefix header, then compiles and links your sources. Depending on how complex your prefix header is, compiling it can take a long time... but the good news is that you only have to do it when you change the file. If you don't change your prefix header, your builds will be much faster.

If someone nags me about it, I'll go into detail about what all the gibberish in the Makefile means. But unless someone does, I'll just assume that everybody is experienced enough with make to figure it out for themselves.

----

Andrew Thompson:

For PCH precompiled headers to work, they must be built using exactly the same options as your project.
If your compilation does not seem to speed up, and you want to check that the precompiled PCH headers are being used, add the following to your Target's Expert View Build Settings:

    
WARNING_CFLAGS = -Winvalid-pch ... along with any other options that are already there


This will give you a warning if gcc finds a .pch file that it cannot use, and explain why it could not be used.

The build settings for your "Precompiled Headers" Target must match those of your main Target. You'll probably need to set at least these Build Settings for the Precompild Headers:

    
OTHER_CFLAGS = -fconstant-cfstrings

If your main target has debugging switched and you are using the Makefile above add:
    
DEBUGGING_SYMBOLS = YES


Naturally you will also want to add any custom flags that your project uses.

**Turning off other kinds of Precompiled Headers**

With gcc 3.3 style 'PCH' precompiled headers switched on, you'll want to turn off gcc 2.95's precomps and gcc 3.1's PFE precompiled headers.

In the Build Settings Expert View set:

    
USE_GCC3_PFE_SUPPORT = NO
PRECOMPILE_PREFIX_HEADER = NO


These settings can also be disabled using the Simple view "GCC Compiler Settings" pane.
