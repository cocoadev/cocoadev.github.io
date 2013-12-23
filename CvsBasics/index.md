---
layout: page
title: CvsBasics
---




**What is CVS?**

CVS is the Concurrent Versioning System, a system that is used to log changes to software projects. This is an important safety measure when collaborating on large projects, but it's also useful for lone developers, because, among many other things, it allows you at any time to go back to the source code of any previous version, which can be a life-saver sometimes.

The CVS homepage is at: http://www.cvshome.org/

**How do I use CVS?**

This page will not give an all-purpose CVS Introduction but rather show how ProjectBuilder integrates CVS support and how to uses that integration.

If you've never heard of CVS before, or are unsure just how it works, we recommend the following links to get you started:


* http://www.cvshome.org/new_users.html - A short general introduction
* http://www.cvshome.org/docs/blandy.html - A longer tutorial teaching you how to use the command line client
* http://www.cvshome.org/docs/index.html - An extensive list of CVS documentation


O'Reilly MacDevCenter Articles:

* http://www.macdevcenter.com/pub/a/mac/2003/07/18/version_control_one.html - Version Control on Mac OS X, Part 1


MacEdition:

*
http://macedition.com/bolts/bolts_20021016.php


Also, _Open Source Development With CVS_ by Fogel is a great introductory book.

Some of its content is available online here: http://cvsbook.red-bean.com/cvsbook.html

**Additional Software:**

There is an open-souce frontend to CVS that is independent from ProjectBuilder. It's called CVL and has its own homepage here:

http://www.sente.ch/software/cvl/

And there's some others :

**MacCVSClient** : http://www.heilancoo.net/MacCVSClient
**LinCVS** : http://www.lincvs.org/ (cross-platform and very complete) 

**Using CVS with ProjectBuilder**

ProjectBuilder offers a feature called SCM (Source Code Management) which right now is just a more user-friendly front-end for the CVS command-line tool.  It allows you to perform common CVS functions such as updates, compares, and commits, right from the IDE.

In order to use SCM, your code must already be checked out from a CVS repository.  That is to say, it must have a "CVS" directory at the top of each directory in the project.  If it doesn't, see SettingUpCvsRepositories.

If your project meets this criterion, ProjectBuilder will display a new column in the Files tab with an icon of a yellow cylinder.  This is the SCM / CVS column.  It tells you the state of each file in relation to the current copy in the repository.

An **M** in this column indicates that  the file has been modified from the copy in the repository.  A **?** indicates an unknown file (one that is not in the repository, and may need to be added).  No mark in this column indicates that the file is in sync with the copy in the repository.

CVS operations can be performed by ProjectBuilder either on an individual file basis, or a group of files at a time.  You can select any combination of files or groups to apply your CVS command to.

The options in ProjectBuilder's SCM menu are:


* Refresh Status

Does the equivalent of a     cvs update .  It talks the repository and sees what files you have modified locally.

* Update to Latest Revision

This is only a partial description.

The Update option updates all the files in your local copy of the source tree with the copies from the repository (if there are newer versions).  If a file has been modified in your local copy of the tree CVS will make it with the M flag indicating that it has been modified.  If a newer version of a file that has not been changed exists CVS will replace your copy of that file with the newer copy (I am unsure if CVS applys a patch of the changes to the file, or if it simply downloads the new copy and overwrites the old one).

Update frequently while working on a project and always update before you modify a file, as someone else (if you have multiple developers working on the project) could have already changed something which will clash with your changes.

* Compare With Base
* Compare/Merge With Latest Revision

The compare/merge options allow you to visually compare the differences between two versions of a source file, and make changes/integrations to the version you are editing, if necessary.  ProjectBuilder uses FileMerge to perform version comparisons and merging.


* Add to Repository

Use this to add a new or unknown file to the repository.

* Commit Changes

If the file has been modified from the copy in the repository, CVS will attempt to commit your changes.  If there are no conflicts, you will be prompted to enter a short description of what changes you made for the CVS log, and the file will revert to unmodified status.


More description forthcoming.

See CvsWrappers for information about storing binary files in CVS.

But don't use the tar wrappers.  They only exist for one version of CVS, and if you end up using someone else's repository (like SourceForge), it just Won't Work.  Just add the nib or project directory and add the files contained therein.


A great article on SVN in xcode 3
http://meandmarkpublishing.blogspot.com/2008/01/xcode-3-feature-version-control.html

