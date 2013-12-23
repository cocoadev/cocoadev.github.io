---
layout: page
title: AGVTool
---

AppleGenericVersioningTool.

see: /Developer/Tools/agvtool help

    
 agvtool - Apple-generic versioning tool for PB projects
   usage:
     agvtool help
     agvtool what-version|vers [-terse]
     agvtool next-version|bump [-all]
     agvtool new-version [-all] <versNum>
     agvtool tag [-force|-F] [-noupdatecheck|-Q]
     agvtool submit [-bytag|-notbytag] <Release1> <Release2> ...
     agvtool what-marketing-version|mvers [-terse]
     agvtool new-marketing-version <versString>
 
 Setting up a project to use apple-generic versioning:
 
     agvtool's purpose is to help speed up common operations
     for Project Builder projects that use the apple-generic
     versioning system.
 
     Project Builder supports a couple different ways to automatically
     embed version info in the products produced by your targets.
     The most supported of these is the apple-generic versioning
     scheme.  You enable versioning support on a per-target basis
     by setting up some expert Build Settings in your targets.
 
     The settings used by apple-generic are as follows:
         VERSIONING_SYSTEM - this must be set to "apple-generic" to
                 enable versioning.
         CURRENT_PROJECT_VERSION - this should be set to the current
                 version of the project.  Versions must be floating
                 point numbers like 57 or 365.8.
         DYLIB_CURRENT_VERSION (optional) - used for frameworks, if this
                 key is present, it will be kept in synch with the
                 CURRENT_PROJECT_VERSION by agvtool.
         VERSION_INFO_PREFIX (optional) - If present, this string
                 will be used as a prefix for the variable names in
                 the generated source file.  If you prefix your exported
                 symbols you will probably want to set this to your prefix.
         VERSION_INFO_SUFFIX (optional) - If present, this string
                 will be used as a suffix for the variable names in
                 the generated source file.  This is rarely used.
         VERSION_INFO_BUILDER (optional) - This defaults to the name of
                 the user performing the build.  This will be part of the
                 generated version string.
         VERSION_INFO_EXPORT_DECL (optional) - If present, this value
                 will be used to declare the variables in the generated
                 source file.  This would rarely be changed.
         VERSION_INFO_FILE (optional) - This setting can be used to specify
                 a name for the source file that will be generated and
                 compiled into your product.  There's usually no need 
                 to change this from its default value.
 
     To enable apple-generic versioning, then, you must set up at least
     the VERSIONING_SYSTEM and CURRENT_PROJECT_VERSION build settings for
     each target you want to be versioned.  A versioned target will have
     two global variables generated and linked into your product.  One is
     of type double and is simply the CURRENT_PROJECT_VERSION.  The other
     is a version string which is formatted to be compatible with the
     BSD "what" command.  You can use these variables in your code if
     you wish.
 
     Projects with multiple versioned targets are required to have the
     same CURRENT_PROJECT_VERSION for each versioned target.
 
 
 Using agvtool:
 
     agvtool should be invoked from a Terminal while you are cd'd into
     your project directory (the folder containing your .pbproj file).
 
     agvtool pays attention to two defaults: CVSEnabled and CVSSubmitByTag.
 
     If CVSEnabled is set to "YES" then agvtool will perform certain
     CVS operations like committing modified project files and performing
     tagging operations.  You can set this default by issuing the
     following command in a Terminal:
         'defaults write agvtool CVSEnabled YES'
 
     If CVSSumbitByTag is set to "YES" then agvtool, by default will
     submit your project by cvs tag using the same version as the
     tag operation.  The sense of this default can be overridden by 
     supplying an explicit -bytag or -notbytag argument to the submit 
     operation. You can set this default by issuing the following
     command in a Terminal:
         'defaults write agvtool CVSSumbitByTag YES'
 
 agvtool commands and options:
 
     what-version|vers:
 
         This command will print out the current version number of 
         the project.
 
         The -terse option can be used to limit the output to the 
         version number only.  With this option there's no output 
         and non-zero exit status if there's an error and zero 
         exit status if all is well.
 
     next-version|bump [-all]:
 
         This command will increment the version numbers of all versioned
         targets to the next highest integral value.  54 will change to 55
         and 234.6 will change to 235.
 
         The CURRENT_PROJECT_VERSION and the DYLIB_CURRENT_VERSION will
         be updated.  The -all option will cause agvtool to also update
         the CFBundleVersion Info.plist key.
 
         If CVS support is enabled, the modified project file will be 
         committed.
 
     new-version [-all] <versNum>:
 
         This command will set the version numbers of all versioned
         targets to the given version number.
 
         The CURRENT_PROJECT_VERSION and the DYLIB_CURRENT_VERSION will
         be updated.  The -all option will cause agvtool to also update
         the CFBundleVersion Info.plist key.
 
         If CVS support is enabled, the modified project file will be 
         committed.
 
     tag [-force|-F] [-noupdatecheck|-Q]:
 
         This command will only function is the CVS support is enabled.
 
         It will create a new tag <ProjectName>-<CurrentVersion> where
         <ProjectName> is the name of the pbproj file (without the extension)
         and <CurrentVersion> is the CURRENT_PROJECT_VERSION with any "." 
         transformed into a "~" (since CVS does not allow dots in tag names).
 
         The -force or -F option, if given, will add a -F to the cvs tag 
         operation.
 
         The -noupdatecheck or -Q option, if given, make agvtool skip 
         the cvs update it usually does prior to tagging to ensure that
         there are no uncommitted changes.
 
     submit [-bytag|-notbytag] <Release1> <Release2> ...:
 
         This command is relevant only for Apple employees.
 
         This command can be used to submit your project to B&I.  Just 
         list the build trains for which you need to submit.
 
         The -bytag option makes agvtool perform the submission by tag
         instead of submitting from the project source directly.  The 
         -notbytag option makes agvtool submit from the project source.
 
         If CVSSubmitByTag is set, -bytag is the default.  Otherwise,
         -notbytag is the default.
 
     what-marketing-version|mvers:
 
         This command will print the current marketing version of 
         the project.
 
         The marketing version is the CFBundleShortVersionString
         Info.plist key.  This is often a totally different version
         determined by product marketing folks.
 
         The -terse option can be used to limit the output to the 
         version number only.  With this option there's no output 
         and non-zero exit status if there's an error and zero 
         exit status if all is well.
 
     new-marketing-version <Version String>:
 
         This command will set the marketing version numbers of
         all versioned targets to the given version number.
 
         The marketing version is the CFBundleShortVersionString
         Info.plist key.  This is often a totally different version
         determined by product marketing folks.
 
         If CVS support is enabled, the modified project file will be 
         committed.


NB:  Do not use AGVTool when the project in question is open in ProjectBuilder.

----
But feel free to use it till you're blue if it's open in Xcode!

*Above may have been a joke, but does/has anyone use/used this tool? If so, can you provide more description? Alternatively, since no one has touched this since the days of PB, should we delete the page? It's pretty much just the man page.*

----

I use it with     /Developer/Tools/agvtool next-version -all; open *.xcodeproj - having the man page on here I think is unnecessary too.

----
Here is a great article on using AGVTool by Chris Hanson: http://chanson.livejournal.com/125568.html

