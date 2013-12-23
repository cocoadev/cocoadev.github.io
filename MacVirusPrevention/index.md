---
layout: page
title: MacVirusPrevention
---

Everyone knows that there are virtually no viruses for Mac OS X. I believe as Mac developers, we can help keep this how it is by exposing areas of the OS that could be taken advantage by virus writers, so that Apple and others can correct them.

I'm not talking about trojans - programs disguised as something else, but when run can do bad things to your system. I'm talking about the ones that can be activated automatically via email or web, or another method.

I've thought hard about how one could download a program, and then execute it by email or web, and I really do not know of any way. It wouldn't be hard to automatically download something, but to then know where it launched would be impossible, even since JavaScript can't determine the user's name (users can change their download directory - not accessible via JavaScript, nor is their username).

Anyways, I'm just trying to see where people would exploit the OS, and I really can't see anything. I know there are tons of people out there who are trying to write a Mac virus, and if it was possible, it would have been done because everyone brags about how they're invulnerable.

----

There are lots of things that Apple apps do that could be taken advantage of. It is quite possible that there is a buffer overflow vulnerability in something like the pdf viewer of the image viewer. If there were, Mail.app opens those images automatically and could spread a virus there. That could run an applescript that would get the names of everyone in your address book and send a message to each. I have no early idea how to do all that...but some do.

I'm sure there are a crapload of vulnerabilities in third party apps too. App makers for the Mac aren't really in the security mindset. I find myself doing things where I think "could this be a security risk?" and then I think "why does it matter?". I eventually have to fix it, but I don't think most developers even think that far.

Since OS X is built on a bunch of little open source programs like say, sshd, which has been hacked/tested/tried/exploited to a degree that rivals windows...they really are going to be pretty secure. You can't really say the same thing for the higher level programs though.

----

I find buffer overflows to be a pretty hard sell for Cocoa apps. Would'nt there have to be a bug in NSData, or NSString to exploit a buffer overrun? I would guess that these classes would have undergone extensive testing over the last 10 odd years. -JJJ

----

Up until 10.3, there was a flaw in NSTextFields where you could crash a program by putting more than X characters in it. That is how people got around the screensaver lock in early 10.3. NSTextFields had been around for quite a while (The NS stands for NextStep)...Plus, there are so many new classes that introduce new security risks. For example, there was a flaw in the Word document reading class apple made. You could run code through a specifically crafted doc file. This was fixed in a security update, but this kind of stuff is found all the time. The only saving grace is, it is usually found by Apple before anyone else. That, and there aren't 10s of thousands of people trying to crack and break all of Apple's stuff all the time.

----

For the most part, buffer overflow vulnerabilities are hard to come by on OS X and PowerPC code, in general.  The reason why OS X software tends not to have these problems is that most software developers who don't know the implications of using some of the C lib calls that have these problems (string copy routines, for example) are the kind of developers that don't use them, anyway.  On that front, we are safe as Cocoa is.

PowerPC code tends not to have as many of those exploits since many of them rely on being able to write null-less code which is hard to do when you have a fixed instruction size that often has null bytes in it.

I think that this is why installers (especially those which require root access) are to be discouraged.  If users become accustomed to elevating the privilege levels of random un-trusted apps, then we have a real problem.

----

In response to statements made about "buffer overflow vulnerabilities": Pride goeth before a fall. We have a real problem.

Currently (April 2006) being discussed: http://secunia.com/advisories/19686/

