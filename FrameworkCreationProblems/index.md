---
layout: page
title: FrameworkCreationProblems
---



Hello everyone :-)

I am trying to create a Cocoa framework to share a document model and view between two applications that use it. The framework and applications compile fine, with no warnings or errors, but when I start the application that has been dynamically linked to the framework, it quits with an SIGSEGV immediately in the objc send message function. If I compile the framework for inclusion into the app budle and the app accordingly (using a copy files build phase) everything seems to work, but the app defaults to the base class of each of my subclassed objects at runtime. None of my code is actually being loaded or used.  Does anyone know why this might be? Could I be building the framework incorrectly? Am I not linking correctly (I thought you just had to add the app to the project and make sure it is included in the Frameworks and Libraries build phase).

Thanks for any assistance you can provide.
Eliot Simcoe

PS. I am running Panther and Xcode... I know...

----

How are you prebinding your frameworks? What is the installation setting for your framework? Is it @executable_path/../Frameworks? --zootbobbalu

----

I didn't know that prebinding had to be enabled for my framework, but now that it is, everything works fine. Thank you very much!
How should this post be disposed of? I am new to Wiki. Is this something worth keeping around for other people who have never before created frameworks?

Thanks again --esimcoe

*May as well just leave it here. Someone might find it by searching if they have problems, and it's not hurting anyone :)*

