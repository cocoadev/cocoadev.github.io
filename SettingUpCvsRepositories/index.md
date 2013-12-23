---
layout: page
title: SettingUpCvsRepositories
---



**Setting up CVS Repositories**

Someone asked on the CvsBasics page how you go about creating a CVS repository if your project is not already in one.

I'm not an expert, but I will explain to the best of my understanding.  For in-depth information, you should refer to the web pages and books recommended on the CvsBasics page.

**Local or remote?**

CVS repositories can be local to your machine, or on a server somewhere.  Web sites such as http://www.sourceforge.net/ offer public CVS service for open-source projects.

But for just learning / messing around, it's best to set up a repository on your own Mac OS X machine.  Here's one way to do it.

**Getting picky**

First, pick a location for your CVS files.  A commonly used location is /usr/local/cvs

Since this directory probably doesn't exist already, you will need to create it.  You will probably need to be root to do this.  First, create "local" in "/usr" if it doesn't already exist.  Mine is owned by root/wheel and has 755 permissions.  Next, create "cvs" in /usr/local.  Mine is owned by stevenf/staff and 770 permissions.  You can exit your root account after setting up these directories.

**The root of all CVS**

All CVS commands require you to specify the location of this repository directory.  As this gets quite tedious, the easiest thing to do is set an environment variable named CVSROOT, that contains the path to your repository (/usr/local/cvs).  Something like "setenv CVSROOT /usr/local/cvs" should do it.  I added this to my .tcshrc file, so that it gets set every time I log in.  

**[CVS init] :)**

Now you have a place for CVS files to go, but you need to initialize the repository.

All CVS commands can be issued by using the "cvs" command line tool.  The first argument to this tool is always the operation you wish to perform.  In this case, we want to initialize the repository.  That command is "cvs init".  That will create some files in the repository, and reset everything in CVS, so use with caution.

**Check this in!**

Now you will need to check in your project for the first time.  This is done with "cvs import".  First, change to your project's root directory.  Note the path to your project.  I've found that cvs doesn't handle project names / paths with spaces in them very well, so you might want to take that into account.  If something goes wrong, you can always wipe the repository directory, re-init and start over.

Here is an example import:  cvs import -m "initial import" projectname stevenf start

In this case, "initial import" is a human-readable comment that will be associated with the first version of this project in the repository.  "projectname" is the name of your project, as it will be known to CVS.  "stevenf" is the "vendor name", you can substitute your own login.  You don't have to use your login (see other reference) but it's easy to remember. Finally "start" is a "tag" for this version of the code, a textual identifier for the first checked-in version of the project.  You can use something other than start, if you like.

You will see a line of diagnostics scroll by, that should end without any errors.

The current state of your project is now "checked-in" to CVS.

**Check it out, too!**

As an experiment, try this.  Change directory to /tmp, or some other temporary directory where you won't clobber your source code.  Type "cvs checkout projectname" where projectname is the same name you used for the import.  

CVS will create a projectname directory, and spew a copy of the current version of the code into it.  You can do this many times.  Your original code is still safe in the CVS repository.

When you are convinced that everything is working properly, you can delete (please make a backup first!) your original code, and then check it out from CVS into your working directory.  Now that the CVS directories are present, you can re-launch your project in ProjectBuilder, and you should see that it has noticed that your code is now under version control.

**For more info...**

See CvsBasics for more information.  In particular, the Fogel book mentioned on that page is highly recommended for full understanding of CVS.  The only reason I know any of this is because of that book.

See CvsWrappers for information about storing binary files in CVS.

-- StevenFrank

