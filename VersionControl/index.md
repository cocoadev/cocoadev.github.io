---
layout: page
title: VersionControl
---



Pages relating to General/VersionControl: [Topic]

General/VersionControl is the process of storing projects in a specialized source code management system. General/VersionControl system store an entire project in a central repository, and provide the ability to extract not only the latest data that was stored, but also every version of every file which was ever stored in the repository. Most General/VersionControl systems also provide other useful features such as the ability to track different branches of the same project, semi-automated merging of disparate changes made by different programmers, etc. http://goo.gl/General/OeSCu

General/VersionControl is an *essential* technology to use for all programmers. Programming without General/VersionControl is like going hiking without a backpack. Sure, you can hold your supplies in your hands, but it's harder and more error-prone. And the worst part is that if you've never really used a backpack, you might not even realize what you're missing out on.

First, let's get the disadvantages out of the way. Yes, there are disadvantages. I believe that they are by far outweighed by the advantages, but it's still best to list them.

* Overhead - in addition to whatever normal programming stuff you do, you have to do extra stuff with your General/VersionControl system.
* Learning - it's One More Thing you have to learn beyond your normal tools.
* Different - you have to change your work habits a bit to work with the new system. Perhaps for the better, but you still have to make the change.


Now, assuming that list hasn't scared you off, here's the big one: why should you use version control?

* General/VersionControl - duh, it's part of the page, right? You can effectively go back in time and grab any version of a file or of your entire project out of the repository. Discovered a bug that you know was introduced sometime after August 19, 2005? Get an exact listing of the changes you made, and narrow down what could be going wrong.
* Backups - typically, you have a single repository which contains all of your projects. This not only provides a second copy of everything, but also makes it easy to back everything up further.
* Collaboration - since everything lives in a centralized repository, if you're collaborating with other programmers, you can all work with the central repository and most things are simply taken care of for you. If you both changed a file at the same time, it will do an automatic merge if possible, and warn you if not.
* Goals - General/VersionControl typically forces you to make manual commits of changes when you feel you're at a good place to save your work. (This is part of the Overhead discussed under disadvantages.) Each commit requires a message that gets stored in the repository to describe what changed. Being forced to get the code to a state where it's ready to be committed, and having to think up a message, makes you keep your goals in mind, and helps encourage finishing one task before moving on to the next.
* Care-free - ever find yourself commenting out huge swaths of code that you're eliminating, because "I might need it later"? Or creating a pre-emptive tarball of your project directory before embarking on major changes? No more! With General/VersionControl, you can pull old versions out of the repository at any time, so you no longer have to worry about deleting something important.
* Branches - Find a critical bug in 1.0, but you're already deep on your way to creating 1.1? Grab 1.0 out of the repository, create a branch, fix the bug there, and then merge it back in if and when appropriate, all without ever disturbing your 1.1 work.


The two most popular General/VersionControl systems today are CVS and General/SubVersion. CVS is the old standby, and General/SubVersion is the up-and-coming. General/SubVersion is highly recommended as it offers everything CVS offers, plus a little bit more, but without breaking from the CVS tradition. With both systems, you can run them locally without setting up any kind of server process, or you can run them on a central server.

----
I agree. General/SubVersion is definitely the newest, fanciest thing. Some words of caution though. General/SubVersion is not as much like CVS as it is claimed to be. Here are the differences that I don't like:

* General/SubVersion is not as easy to setup as CVS. It prefers (but does not require) both Apache2 and PHP. You'll have to do some moderate work to get both of these working on a Mac.
* The General/SubVersion repository is not like CVS. Everything is stored inside either a database or binary data files. In my experience, binary data always get corrupted eventually.
* The General/SubVersion root is not like CVS. I supposed I could get used to it, but it is some funky URL thingy.
* General/SubVersion mandates a specific directory structure (tags, trunk, etc) for projects. I didn't like it.


Bottom line - General/SubVersion is highly recommended by General/SubVersion fans. It has a similar command set to CVS. Some things it can do better than CVS. Some things CVS can do better than General/SubVersion. Either one is better than nothing at all. Some people prefer CVS.
----

Well, I've been using Subversion on my project for a while now, and while I've never used CVS, I'd say Subversion is the best tool for the job. I'll play devil's advocate on the points above:


* *General/SubVersion is not as easy to setup as CVS. It prefers (but does not require) both Apache2 and PHP. You'll have to do some moderate work to get both of these working on a Mac.*
I don't know about CVS, but Subversion isn't that difficult, especially if you only need a local repository; if you don't want to mess with the server side stuff, then there's services that offer SVN hosting.
* *The General/SubVersion repository is not like CVS. Everything is stored inside either a database or binary data files. In my experience, binary data always get corrupted eventually.*
Unlike CVS, Subversion commits are atomic ('all or nothing'); while earlier version did have issues with locks and some corruptions these were due to the Berkley DB system used, hence the reason there's now also the binary file option, which has solved these issues. Since commits are atomic, corruption of the binary data file is rare (if not impossible :)
* *The General/SubVersion root is not like CVS. I supposed I could get used to it, but it is some funky URL thingy.*
That argument applies both ways (or for any softwares that have similar functions in fact).
* *General/SubVersion mandates a specific directory structure (tags, trunk, etc) for projects. I didn't like it.*
That's incorrect. You can setup the directory structure however the hell you want. The documentation clearly states that chosing a specific directory structure is a 'human thing' and not a technical requirement.

--Seb

----

Git is another version control system that has appeared--February 2008 General/MacTech (in print) http://www.mactech.com has an article on it. The key difference is that respositories can be held on each individual's system rather than a central server. Users commit to their own repository, and user repositories may be synchronized, peer-to-peer.  This is called a *distributed* version control system.  One advantage is that if the server crashes or otherwise becomes inaccessible to you, you don't lose the project history.

Other recently developed distributed version control systems include Bazaar http://bazaar-vcs.org/ and Mercurial http://www.selenic.com/mercurial/wiki/ .



Perforce is a well-established, commercial SCM, supported by Xcode. http://www.perforce.com/ 

----

How about distributed versioning systems? Git? Mercury (hg)? General/BitKeeper?
