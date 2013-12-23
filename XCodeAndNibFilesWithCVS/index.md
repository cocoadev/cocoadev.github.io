---
layout: page
title: XCodeAndNibFilesWithCVS
---



This page was started with the following goal: be a guide to a newbie who wants to use CVS, who is somewhat familiar with the basics now (after reading the seeAlso pointers), but is still wondering about nib files and the use of them in General/XCode SCM. This is who I was yesterday. So the following guide is clearly not a reference, and hopefully, other people will improve it. General/CharlesParnot.

This page should answer the two questions:
How can I get nib files to be properly handled by CVS?
How can I get nib files to be properly handled by the SCM system in General/XCode?

I don't think I really address here the question about nib files when the project is on a server, like getting a project from Sourceforge. Then I guess the use of SCM from within General/XCode gets tricky and other issues might show. Also, I only tried all of this in General/XCode, so I don't know how it works in Project Builder (probably things are even worse). Finally, General/XCode (or whatever name it will have) might handle CVS better in the future, thus the name General/XCodeAndNibFilesWithCVS.

----

First, a comment about nib files. They are really packages, i.e. are in fact directories that contain files. You may not be aware of that if you only look at them in the Finder, as they will show as plain normal files. You can have a peek inside by control-clicking and select 'show package contents' in the contextual menu. You can also go to the Terminal, and cd into them.

Now, a comment about CVS, the command-line tool. In its latest versions, it seems to be able to handle binary files quite well. I don't know about resource forks, but I suspect it does not handle them. If you have started a recent project, with recent files, there should be absolutely no problem using binary files, and there should be no problem using nib files, as CVS will just treat them as directories.

So now, back to the initial questions, here are the solutions I found.



*
**Only use CVS from the command line.**

This is probably the simplest way to go, given the limitations of SCM from within General/XCode. The first time you import your project, nib files will be properly handled by CVS (however see otherRelatedIssues, about backup nib files). You work on the project in General/XCode, make some changes to the nib files. When you are done, every day, every week, whenever you feel like it, you commit the changes from the command line. For example, if you issue a commit command for the project directory as a whole, the cvs tool will recursively commit the changes for all the files that were changed, including the files inside the nib packages.

*Things still unclear (please answer these questions and remove them from the list): how does cvs handle the version number if you commit only one of the file inside a nib package? Is the version number of the nib package/directory changed too? Can I do that by commiting the nib package/directory? Do you really even need to worry about it?!*

When you create a new nib file in General/XCode, you have to add them to the repository, using the add command. As the add command is not recursive, you have to add the directory, as well as the individual files with separate instructions.

*
**Use CVS from within General/XCode.**

It is not clear how General/XCode knows that you are working with cvs, but probably it can detect those CVS folders that the cvs tool puts in every folder. It works fine with text files, such as source code files. General/XCode will show their status and history, will mark them as 'M'=modified or '?'=new as appropriate, and will then consequently allow you to 'Commit' or 'Add' the corresponding files. All of these commands are available in the Application menu 'SCM' or in contextual menu.

However, all of this does not work for nib files. It does not show the CVS status properly, i.e. if you modify a file, it does not put the M in the SCM field of the table view of files, nor does it add a 'Commit' item to the SCM menu or the contextual menu for this item. You can still commit from the command line of course, but you can't get the information about CVS status from General/XCode (no version number, no history).

The first thing that I tried to fix that did actually work. That was adding the '.cvswrappers' file to my home directory, as suggested by Apple (read below seeAlso).
Though cvswrappers *"were a hack to an earlier version of CVS that nobody uses anymore"* (quoting somebody on another page of this wiki), they are apparently still used by General/XCode as a way to manage nib files in CVS. This is why an example cvswrappers still ships with the developer tools, and why it is still suggested for use in Apple's FAQ. So, these "hacky" cvswrappers get even hackier in General/XCode (can you say that in English?). You just do what Apple say, you now get the correct behavior for nib files. Their status and history is properly displayed in General/XCode, and you can commit them. I suspect you can also add them from the GUI.

So should you do that trick and use CVS from General/XCode? I don't know if this is really worth it. If you work on a project on Sourceforge, it is definitely not recommended, and will not work unless it is clearly handled that way. If you are the only user and work on a local repository, the command line is quite simple anyway, and General/XCode does not really add anything. On the command line, you can commit the whole project when you feel like it, and you can choose any version numbering scheme. Within General/XCode, commiting the whole project is awkward (select the project in 'Groups And Files', and select all files in the Files pane, and Commit), and General/XCode will just allow standard numbering of revisions (1.1,1.2,1.3,etc..) and does not permit tagging (for release numbering).

*Things still unclear:  I also found that CVS in General/XCode does not work so well with some of the resource files (such as gif,...), though it works with credits.rtf. It shows their status, but does not allow to commit. I did not check yet if cvswrappers fixes that behavior as well.*


*
**Other solutions**

*Mix both approaches. Commit source files from General/XCode. Commit from the command line once in a while to have your nib files in the repository, and also change the version number when you need to.
*Use a GUI frontend for CVS. I have only been using CVS for 24 hours, so I cannot really comment too much on that,  but my feeling is that it may be nice. Instead of using the cvs in the terminal, you use CVL, a GUI frontenf for CVS. You still have to use two separate programs (General/XCode and CVL, instead of General/XCode and cvs). I used CVL a little bit, and it seems to have most of the funcionality of CVS that I need for a local project, including tagging files, and even have a recursive add which makes dealing with nib files much easier. CVL can be found at http://sente.epfl.ch/software/cvl .







----
**otherRelatedIssues**


*
Backup nib files. If you import a project that you had already been using before starting CVS, it may have some backup nib files that come with a tilde (e.g. "General/MainMenu~.nib). Upon import, cvs will add these files to the repository, and they will be in the project that you check out. The problem is when you modify a nib file, the backup *~.nib file is recreated from scratch. Remember the nib file is in fact a directory, so the initial *~.nib file had a CVS folder inside. This CVS folder is gone when a new backup *~.nib file is created. So the cvs system will be lost, if you try to commit the whole project from the command line (that would not be a problem with cvswrappers, but you don't want those backup files in your repository, anyway, do you?).

The solution to this is to not import these files in the first place. You can add a '.cvsignore' file in your home directory with the following content ".DS* *~.nib"  (read below seeAlso). This will also take carre of the .DS_store files used by the Finder that you don't want in the repository. If they are already imported, you can remove them from the repository (e.g. remove the 'General/MainMenu~.nib,v' files).

*
Renaming nib files.
*I am not sure how to do that, but I think it is a general problem with cvs. Can somebody help here?*

You need to do surgery to your repository:  First make a backup of your $CVSROOT directories, just in case.  (a simple tar into another file somewhere is sufficient).  Go into $CVSROOT/your-project/whatever where your nib file lives.  mv the directory from the old name to the new name.  In your checked-out projects, you'll need to edit the CVS directory in your /whatever directory.  Change the D/General/MyDocument.nib////  line to be your new name, like D/General/CoolDocument.nib////  Then do a cvs update and cross your fingers :-)





----
**seeAlso**


*
Apple FAQ: http://developer.apple.com/documentation/General/DeveloperTools/Conceptual/General/IBTips/Articles/General/FreqAskedQuests.html
particularly this answer:
*
(Q): I have problems adding nib files to my CVS repository.
(A): The easiest way to add a nib file to a CVS repository is to use the cvswrappers tool. Create a .cvswrappers symlink in your home directory that points to the cvswrappers file located in the /Developer/Tools directory:  ln -s /Developer/Tools/cvswrappers ~/.cvswrappers  You can now add nib files to a CVS repository the same way you do with regular files.
*

*
hint for the backup nib files: http://cocoa.mamasam.com/COCOADEV/2002/09/1/44043.php

*
On this wiki: General/CvsBasics, General/CvsWrappers, General/SettingUpCvsRepositories, General/XCodeWithCVS

*
Apple documentation on cvs: http://developer.apple.com/documentation/General/DeveloperTools/cvs/cvs_stoc.html

*
A method for making NIB files work under Xcode SCM with Perforce can be found at General/XCodeAndNibFilesWithPerforce



----
I have to admit that even though I am a command-line junkie and always use CVS from the command-line when using Linux, I actually find General/MacCVSX to be quite a nice GUI for CVS.  It's available from http://cvsgui.sourceforge.net

Don't even bother with the SCM stuff in General/XCode.  It's totally counterintuitive.  I actually like dealing with SCM separately from the IDE so that I can focus on coding when I'm using the IDE and focus on SCM later when I use my SCM management tool.

----
**Comments**
