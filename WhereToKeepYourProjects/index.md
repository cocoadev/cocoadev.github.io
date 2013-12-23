---
layout: page
title: WhereToKeepYourProjects
---

This is how I have my programming stuff organized. It works for me, and I thought others could benefit from it too. This is just a suggestion - as always, use whatever works for you.


***~/Developer/** Root folder for all personal development-related stuff.
***~/Developer/Projects/** Keep project and your own code files here.
***~/Developer/Builds/** Set General/XCode to place build products in this directory.
***~/Developer/Applications/** Where to keep programming-related applications you've downloaded or created (e.g. General/DocoaBrowser, Accessorizer &c.)
***~/Developer/Tools/** For development-related command line tools (e.g. autodoc, class-dump, miscellaneous shell scripts)
***~/Developer/General/SampleCode/** For sample code you've downloaded (from Apple or elsewhere.) Also a good place to make working copies of sample projects from the /Developer/Examples/ hierarchy.
***~/Developer/Documents/** 'How To' documents; stuff that isn't really sample code. You might also want to keep icons and graphics for your projects in here. I keep them in with the project folder.
***~/Developer/Distribution/** Where to create disk images and archives for stuff you're going to release into the wild.


----

It was the case that Project Builder, under certain circumstances, would look for user-specific information in ~/Developer. It looks to me like Xcode is trying to move stuff into ~/Library/Application Support instead, so maybe this no longer applies. Still, you might want to make sure you don't inadvertently install something that confuses Xcode. Or if Xcode wil take advantage of certain things in ~/Developer, by all means use it. 

-- General/MikeTrent
