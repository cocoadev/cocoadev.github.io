---
layout: page
title: ReadingTarFiles
---

I'm looking for a way to read tar (and tar.gz) files without using NSTask, fork or exec. I'm only interested in a listing of the contents, so some kind of data structure containing the equivalent of     tar -tf file.tar would be nice. Is there a C API for tar? /usr/include/tar.h was not very helpful.

--TheoHultberg/Iconara

*How about libtar?* [http://libtar.darwinports.com/]

Seems to be exactly what I was looking for, thanks. I'll report back if I get it to work. 

What I really would like next is a step-by-step guide to making frameworks out of unix libraries (like libtar).

--TheoHultberg/Iconara

Hear, hear! I've recently wondered about the same thing, since I use an open source dynamics library with a pretty complicated makefile, which builds to a static library. I'd like to edit the makefile to build a framework ( when on OS X ), and while I imagine I *could* just have the makefile make the folders and subfolders, make symlinks and copy a dylib and so on, I'd like to know if there's any streamlined way to build frameworks outright. Plus, I'd have to take into account the "@executable_path/../Frameworks" factor as well, since I want my framework packaged with the app.

--ShamylZakariya

I was just about to say that "there's some discussion on StaticVsDynamicLinking about this, but nothing really useful" when I realised that it was you who started that discussion... Well, we're in the same boat, you and I.

I've tried to find starters on how to do these things, and found out how to build static and dynamic libraries, but I have no idea what the difference between them and frameworks is.

--TheoHultberg/Iconara

----

If you link against a static library, you don't need to (and shouldn't) distribute it with your application.

