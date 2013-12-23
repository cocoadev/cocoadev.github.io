---
layout: page
title: CvsWrappers
---

CVS only offers its version control services for text files.  CVS must be told which files are binary, so that it knows to leave them alone.  For a better explanation of this, refer to the more in-depth CVS documentation listed on CvsBasics.

One way to achieve the separation of text and binary files is with a file called cvswrappers.

If you have already set up your repository (see: SettingUpCvsRepositories), you already have a default cvswrappers file, but it doesn't do very much.

To see it, go to a temporary directory such as /tmp and check out the "CVSROOT" project.  This project lives in your repository directory, and can be checked out just like any other project in CVS.  The CVSROOT project contains configuration information for CVS.  

If you change directory into your checked-out copy of CVSROOT, you can see the default cvswrappers file. Refer to other documentation for the exact format of this file.

Apple provides a replacement cvswrappers file you may wish to use.  It offers such conveniences as the automatic 'tar'ring of NibFile directory structures into a single binary file.  It also provides default behaviors for common file extensions, such as .tiff, .jpg, and .strings


Apple's cvswrappers lives in /Developer/Tools/cvswrappers.  To use it, copy it over the cvswrappers in your checked-out copy of CVSROOT, and then commit the changed file back to the repository.

-- StevenFrank


Does this still work in 10.2?

I get the following when I try to use a repository that I've checked /Dev/Tools/cvswrappers into.

cvs [server aborted]: -t/-f wrappers not supported by this version of CVS

% cvs -v

Concurrent Versions System (CVS) 1.10 `Halibut' (client/server)

see: http://www.mail-archive.com/info-cvs@gnu.org/msg22197.html

anyone have a solution for putting nib dirs into a remote pserver: cvs repository under 10.2?

-- TomWaters

----

Yep.  Don't mess with the tar cvswrappers things.  IB, PB, and Xcode all respect the CVS directory.  Just add your nib file as a directory, and then add the contents with the -kb flag, and you're golden.  I use this all the time, and with linux-based CVS servers, and it works.

----

I tried to compile some information about nib files and CVS in XCode. See XCodeAndNibFilesWithCVS. It probably needs some editing. CharlesParnot

----

Erk!  I tried the cvswrapper thing as well, as it seemed the sensible thing to do, and now sourceforge produces the following error for everything:
cvs [server aborted]: -t/-f wrappers not supported by this version of CVS
I can't even delete or revert - same ol' error.  Any fix for this kind of thing?

Read the entry a couple of things up from here.  Just don't mess with the tar cvswrappers things.  Add the individual files in the .nib and .xcode directories.

----

I consider Apple's /Developer/Tools/cvswrappers to be a good start. However it treats .nib directories as binary files which got me in trouble when tracking 3rd party sources with vendor imports.

comment out the line:

<code>*.nib -k 'b' -f '/Developer/Tools/cvs-unwrap %s' -t '/Developer/Tools/cvs-wrap %s %s' -m 'COPY'</code>

in the <code># Wrapped files</code>. section

and add:

<code>classes.nib -k 'b' -m 'COPY'</code>

<code>info.nib -k 'b' -m 'COPY'</code>

<code>keyedobjects.nib -k 'b' -m 'COPY'</code>

<code>objects.nib -k 'b' -m 'COPY'</code>

to the <code># Binary files</code>. section.

-- DanielEricsson

