---
layout: page
title: WritingYourOwnFramework
---

I have been toying around with creating my own framework to share code amung a suite of tools I am writing. I create a new framework type project, wrote a bunch of classes, compiled it and included it in an application. Now how do I access the classes? I tried this:
    
#import <General/NameOfFramework/General/NameofClass> 

But that doesnt work. What do I need to do? Im under the impression from my compiler errors (or is that the linker?) that I need a file that #defines all my classes for the framework.

----

What are the preprocessor/compiler/linker errors you're getting? -- General/AdamAtlas

Header file not found. no macro name given in #define directive

----

Be sure that in the Target Settings for your framework, you're specifying that the headers you want public are public.  Furthermore, make sure the framework is in an acceptable framework-y place such as ~/Library/Frameworks (you may have to create the directory).--General/JoeOsborn

----

 http://developer.apple.com/techpubs/macosx/General/DeveloperTools/General/ProjectBuilder/General/AboutBox/General/AboutBox.pdf

-- zootbobbalu

----

Weird. I had a previously compiling project stop compiling, giving me these same kinds of errors (header file not find, no macro name given). The framework works if I add it to new projects, but not in this one project. Even if I remove and re-add it. Spontaneous Project Builder setting rot?

-- General/AdamVandenberg
