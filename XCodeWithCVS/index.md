---
layout: page
title: XCodeWithCVS
---



I'm trying to get General/XCode working with CVS.  I don't even mean necessarily using the CVS GUI stuff that's built into General/XCode.  The command line will do.  I've started by copying /Developer/Tools/cvswrappers to CVSROOT (see General/CvsWrappers).  And that helped, but now, after I checkout the project and make  a change to the General/MainMenu.nib, I run a "cvs update" and then a "cvs commit" and get: 

    
cvs server: Up-to-date check failed for `English.lproj/General/MainMenu.nib'
cvs [server aborted]: correct above errors first!
cvs commit: saving log message in /tmp/cvsnadJiU


----
Here is my advice to you.  Remove General/MainMenu.nib from your CVS repository.  It's probably fried.  Now, make sure you do indeed have the cvswrappers in your CVSROOT.  Next, copy the same wrapper file to your home dir under .cvswrappers (yes, it is invisible).  Now, add the nib file again, and it should work.

----
Note -- removing General/MainMenu.nib from a CVS repository isn't all that easy:  First you have to rename it in a working copy, then cvs remove General/MainMenu.nib, then cvs commit.  However, it's not gone yet -- you can't then rename it back and cvs add it, because CVS commit gets upset there's a file and a directory of the same name.  As far as I know, you have to go into your repository and remove General/MainMenu.nib,v from your Attic. This is the only case I've ever found for having to modify my repository. A CVS expert should vet this instruction.

----
Here is my advice to everyone.  Forget .cvswrappers for nib files.  They were a hack to an earlier version of CVS that nobody uses anymore (especially for places like sourceforge).  Apple doesn't even use .cvswrappers.  Apple's tools respect the CVS directory for nib files and Project Builder / Xcode files, so just add the wrapper directories and the files inside.

----
Can anyone point me (a CVS newbie) to (or simply describe) a how-to for using the GUI CVS in General/XCode?  I.e. setting up a project to use CVS, where to save various files, etc.

----
I'm also a CVS newbie but I have managed to get my project into CVS. I'm not sure this is the best, only or even correct procedure but it did seem to work!


*
Created my project in Xcode...say in directory **foo**
*
Added my source files to the project
*
Quit Xcode and then used the cvs **import** command to import the directory **foo**, project file and all, into the CVS repository
*
Renamed the directory **foo** to **bar**
*
Used the cvs **checkout** command to check out the directory **foo** in the original location. This is why I renamed it before this step. That way if anything goes wrong I haven't overwritten anything important. Once I'm satisfied that things are ok, I can just delete **bar**.
*
Launch Xcode and open the project in the new **foo** directory
 

At this point Xcode seems to realize that these are CVS files and works correctly (assuming you've turned on SCM in the preferences, of course).

The entire procedure seems overwrought and a bit screwy to me, but I'm an expert on neither CVS nor Xcode. It would be nice, however, if this could be done entirely within the Xcode GUI for those of us who don't want to have much to do with CVS.

----

Another CVS w/ General/XCode newbie here.  My process was almost the same as the previous poster's except that I used cvs add/commit instead of cvs import.  Is there any fundamental difference?  General/XCode doesn't seem to be recognizing the CVS status of my project.  Any good pointers to info?

----

Yes, I believe so.  As far as I know, "cvs import" you only use to "import" an entire project into the CVS repository.  Once you do that, then you use "add, update, commit" to make changes and get changes to the project.

----

I've done lots of CVS with Project Builder and General/XCode and never had any trouble with any of them. I basically follow the list of steps outlined above. These are not "screwy" -- they are the steps as outlined in the CVS documentation. Once you have a checked out project, PB/General/XCode handle them fine. If there are GUI tools for CVS in General/XCode (never looked, I just use command line), they no doubt work identically: create repository, create project, import, rename original directory, checkout directory, run IDE, voila. I have no problems with .nibs and I don't treat them specially (so I have CVS directories in the bundles, oh well, they work fine). -- General/BrentGulanowski

----
The steps above don't really apply for me.  I don't want to create a new project.  I want to use Xcode to work with an already existing cvs module.  I've tried checking the module out, and then creating an Xcode project where I add the files I checked out.  Nothing I do seems to make Xcode recognize these files as having been checked out of CVS.

-- OK, sorry, misunderstood. General/XCode will look for a CVS directory in whatever directory the files are in. If the project file itself is not part of the repository, that may cause a problem. I've never tried that situation. If the project file itself is not part of the module, then there may be an issue -- perhaps that's the "clue" General/XCode uses to recognize cvs is in use, as opposed to just scanning for the directory arbitrarily. If so, you may be out of luck. Ask the person/people who manage the repository to check in the project file which you have created. Until then, stick with the command line tools to keep the files up to date. �Brent

----

Hi . I am new with this site. I am trying to do exactly what you said . I am using an existing repository located on a Win2000 box
and would like to create a new General/XCode project with all the files in there (no General/XCode project file though). 
Does anybody have experience with such a situation ? 
Thanks ! 

----

So, do we forget about cvswrappers or not?  Will checking a nib in/out using General/XCode work properly with cvswrappers NOT configured?  All the docs I've found online say that you HAVE to configure cvswrappers up using the file provided by Apple.  So if this is a hack for an earlier version, why doesn't anyone else mention that?

And how will I know if it is/isn't working?  (I haven't configured CVS yet as I want to have everything in place before I start checking projects in/out.)

Thanks.

----

You do *not* need cvswrappers.  It is an old dead hack.  Forget you ever heard of it.  General/XCode and IB do the Right Thing regarding the CVS directory in nib files.  Just add the nib file directory and the individual files contained therein.  I use CVS every day for my Cocoa programming and do not have the cvswrappers.

*I was not able to get it to work *until* I added cvswrappers.*

----

If you add the nib file directory, do you also have to add each individual nib file?  This seems odd.  Is this how CVS works for ANY set of directories/files under that directory?

----

Yep.  It kind of breaks the 'directory as a file' abstraction, but as programmers, we can Handle That (since we have to deal with that in code ourselves)  And yes, that's how CVS works.  You can cvs add a directory, then cvs add and cvs commit the files inside of that directory.
  
----

So then why add the directory at all?  Why not just add the files?  Is it simply because CVS won't checkout the file to the correct location if its directory hasn't been CVS added yet?

And that said, what if I create a new file in General/XCode and then try adding it from there?  Does its directory have to be added first?  Can that be done in General/XCode or does it HAVE to be done at the CLI?

----

� Well you can't create directories from within General/XCode, unless you count the file browser, in which case, yes, you have to add the directory; you have to add every directory (that's just *the way it is-TM*). But now we're talking about two different things. If you just create a new file *in an existing directory*, General/XCode can add that to the repository just fine.

As for the original problem, someone missed, glossed over, or silently discounted my last point, relating to getting General/XCode to recognize files as part of a CVS � *if the project file is not in the repository, you're not going to get General/XCode to recognize the CVS*. Now, that's a *theory*, so could somebody either confirm or deny it? As for the OP, if you have an General/XCode project, did you add it to the repository? Do you have write access? If not, did you ask the maintainers to add your General/XCode project? Did that work, or not? �*Brent*

----

I also have a CVS with General/XCode problem.  I've put it in this discussion since it would not make sense to start another thread.  Here's what I've done and what isn't working for me:

I have an General/XCode project folder called:  **~/Development/General/MyProject**     and I've created a CVS root folder called:  **~/Development/CVS**  and in my .profile file I've added the line:  **export CVSROOT = "~/Development/CVS"**  I've relaunched terminal and a simple export command shows the CVSROOT variable as holding the value "~/Development/CVS"

I initialized the CVS folder with:  **cvs -d /Users/General/MyUser/Development/CVS init**   and when I did this it created a CVSROOT folder under the CVS folder.  I can only assume that this is working.

Now I cd'd into my project folder and at the command line typed:  **cvs import -m "General/MyProject" General/MyProject General/MyCompany START**  based on some examples I got off the Internet.  Other examples, including the ones at Apple show only:  **cvs import -m "General/MyProject"**  The only way I could get it to work is if I typed it the way I did.

Now, I deleted the original General/MyProject directory and at the command line (while in the Development directory) typed:  **cvs checkout General/MyProject** and sure enough, it checked it out and created the new directory with all the files in it.

BUT, when I open the project in General/XCode, with SCM turned on, the funniest thing happens.  Nothing.  For example, I modify one of the C header files and save it.  When I look at the SCM window it does not list this file as **M**odified.  In fact, the only file it DOES list is:  **General/MyProject.xcode/General/MyUser.pbxuser**

Can someone PLEASE tell me what on earth I am doing wrong?  I just can't seem to find a tutorial (here or anywhere else) that seems to work!

Thanks.

----

Not answering the above, here is a good tip on using CVS and General/XCode: if your project is not local, but on a remote server, you probably use SSH to connect to it. It is really annoying to type your password every time you want to check in or update. Usually you create ssh-keys to solve this problem, and there are lots of tutorials on how to acomplish that (just google for "ssh key tutorial"). The problem with General/XCode is that it want's to use DSA-keys, not RSA, which is the default in SSH. So be sure to create a DSA key, not a RSA, or create both.

Another thing is that the authorization panel that is brought up in General/XCode the first time you check out or update does not ask for the password to the server, but to the password to the DSA-key, although the text on the panel doesn't really mention this.

--General/TheoHultberg/Iconara

----

Could someone offer any insight on the above scenario re: how to get the project into/out-of CVS in the first place?

� I don't know what to tell you. I just tested it here and it worked fine. I have a little test project called General/CocoaBox and I did this:


*cd into project directory
*type "cvs -d /usr/local/cvsroot import -m "test message" General/CocoaBox base start" where base is my vendor tag and start is my branch tag
*go to a different directory
*type "cvs -d /usr/local/cvsroot checkout General/CocoaBox"
*open General/XCode
*right click on the file list in the main window and select "SCV" so it shows CVS status
*add a comment to file and save � "M" appears in SCV column as expected


Now this was with an existing repository. So all I can guess is that you have some kind of a permissions problem or something else wrong in the repository, but I don't know. Note that I do not use CVSROOT environment var because I have a lot of cvs checkouts from all over the internet and don't want a conflict.  �General/BrentGulanowski

----

Great!  Now, all of a sudden, it works!  But what about .nib files?  I've modified a .nib and its SCM status never changes.  I look at the control-click menu and "Add to Repository" is available so I click it and nothing happens.  Then I mod the .nib again and still no change to the SCM status.   What am I doing wrong, now?  Do I NEED these wrappers to have General/XCode notice the change as far as CVS/SCM is concerned?

Thanks again.

----
I too fought for a day with the problem. I just started to use CVS yesterday, and never had before, so take this answer with some appropriate reservations...

I found that the problem with .nib files is somewhat subtle and the answer to how they should be used is not unique. Because the information out there is very sparse, and the issue likely to evolve in the future, I thought it was worth starting a new page for that, so read General/XCodeAndNibFilesWithCVS.

----
I couldn't get anything up and  running. No Hint of the above seems to work.
But I found a hint in the online manual.- Here is a description, of what now works fine for me (not as good as within Eclipsed, but a good start)
 - I just want to work within a PHP-Project with General/XCode - Which seems to be a little bit more comfortable as using General/TextEdit -  

*Open General/XCode, create a new empty project and put it anywhere.
*Select your project and press the big - blue "i". This brings the inspector for the project up.
*On the first page the is an entry for a SCM-System. Just select CVS and the SCM is enabled.
*Then I added the root-directory of a previously checked out PHP-Project. Menu Project --> Add files...
Option were: (did not checked "Copy items into destination group's foulder..."), Reference Type: Relative to Enclosing Group, Text Encoding Unicode (UTF-8) (hope this is a good choice maybe others are better - this needs a little bit more testing) and Recursively create groups for any added folders
*The orginal files are not copied an resides still in the checkout CVS-Directory.
*I could edit any file and from the SCM menu I could commit my changes. (After I chose Refresh SCM status for the project. - But I don't know If it needs to be done)


That's it,
hope that give us new ideas on this topc.

Oh, I see, that this seems only to work for files with the suffix .txt - My .php - files are still unknown to the SCM :-(

Maybe anybody else knows what to do. Change the file-type list?

-- and again new informations: It seems not to be a good solution, because changed file are not automatically refreshed, I have to do this still with a shell and newly added files have to be added by Adding via Project - Menu.

I think, I give up an. :-(

----
Re the above comment: Does anyone know how to set up General/XCode so that the default setting for new files added to a project is "Relative to Enclosing Group"?

I share a source code tree between multiple computers via an NFS share and have had no end of trouble with source files using an absolute path when they are first added to my project.
