---
layout: page
title: XGridDummyPlugin
---

**Warning**: this is completely deprecated and useless now, since Xgrid TP2 can not be downloaded from Apple anymore, is not supported at all, and has been supplanted b y Xgrid 1.0 in Tiger and Xgrid 2.0 in Leopard.


----
----
----
----
----
----



----
**Warning**

The following is not officially documented by Apple. This means it should not be used and it may change anytime!

I did not read anything about BEEP before writing this and maybe I should have...

----

tableOfContents :

part1 - Submitting jobs

part2 - Reading job results

part3 - The General/DummyXG plug-in example

references: some links

comments: all yours
----

I started using General/XGrid to run a foundation tool written in Cocoa. Of course, as I am familiar with Cocoa, I wanted to write my own plug-in rather than using the 'Create costum plug-in' plug-in. That gives you much more flexibility. If you are familiar with Cocoa, it is also very easy, particularly if you read the rest of this. Apparently, you can learn General/ObjC in just a day or two if you already know C, so it should be easy even for people that don't know General/ObjC!!

When I started writing the plug-in, I was soon stopped by the lack of documentation. Based on the shell plug-in template, you can only write a plug-in that uses a binary present in all the agents (you cannot download the executable to the agents), you have to use only the STDIN, STDOUT and STDERR to move data back and forth and you cannot have arguments for your commands. All of this could have been worked around by a clever use of shell scripts and some curl commands together withe the 'Create custom plug-in' plug-in, but I wanted something simpler and more general.

By reading the whole contents of the xgrid-users mailing-list at Apple (only 2-3 months old, fortunately!) I could get a very good starting point from two postings (see ref2 and ref3). Thanks to Daniel and Jim!! As suggested, I used tcpflow to get more info by running a 'Create custom plug-in' with different parameters. I also found how to get some files back after the tasks are finished. Finally, I think I understand better how to use multiple jobs to save intermediate output files and run more than 251 tasks without changing any UNIX settings.


----
*back to tableOfContents*

part1 - 
**Submitting jobs** 
----
You submit one job to the client with the snippet1 :

    [self submitJobWithParameters:jobParameters];

where     self is the instance of your     General/XgJobViewController subclass, and     jobParameters is the dictionary with the parameters for the job. I only know of one key for this dictionary, which is '    General/CommandLineDictionaries'. General/XGrid expects the object for this key to be an array of dictionaries. The key is documented in Apple's template, and it is not showing in the tcp transmissions. The undocumented keys I found are thus only for elements inside one command dictionary.

**Job description**

Here is the most complete dictionary (as far as I know) that can be used for one element of the     General/CommandLineDictionaries array, the XML way:
    
<key>Command</key>
<string>this/is/the/executable/path</string>

<key>Arguments</key>
<array>
	<string>hereisargument1</string>
	<string>andargument2</string>
	<string>and_oh!_afilename</string> etc...
</array>

<key>Inputs</key>
<dict>		
	<key>General/TarredExecutable</key>
	<data></data>
	<key>General/TarredWorkingDirectory</key>
	<data></data>
	<key>General/TarredWorkingDirectoryName</key>
	<string>directoryName</string>
	<key>General/TarredFiles</key>
	<data></data>
	<key>General/StandardInput</key>
	<data></data>
</dict>


Now more details about the undocumented stuff (the     General/StandardInput and     Command are explained in Apple's template and are easy anyway):



*
**The General/XgTarWrapper class.**
You can use it to generate all the General/NSData objects for the     General/TarredExecutable,     General/TarredWorkingDirectory and     General/TarredFiles.

*
**The executable.**

The     General/TarredExecutable can simply be the tarred binary for the executable, which gets transmitted to the agent. It can also be a whole directory, with your executable inside it. How does General/XGrid know how to call it then? This is very simple. It looks at the     Command key. If it is an absolute path, it does not use the tarred executable but the corresponding local binary found at that path in the agent, if any. If it is a relative path, it is relative to the folder created by the Xgrid agent in /tmp (with a name like     xgagent.TI0bU9aX). So if you only tarred the binary, just use its name. If you tarred a whole folder, provide the relative path to the binary inside it.

Why would you tar a whole folder and not just the binary? For intance, if you have libraries whose loading depends on their path relative to the executable. For example, I use a Foundation tool that needs a private framework. Normally, the tool is inside an application package (in Contents/General/MacOs/ together with the application executable) where there is also the framework(in Contents/Frameworks). The tool can be used at this path *as is* (of course, I also have a symlink to it in /usr/local/bin). As the framework was built to be private, it can only be loaded by an executable if its path is @executable/../Frameworks; so the framework really needs to have the same parent folder as the executable. It does not work if I just put the framework in the tarredDirectory.

Having a whole directory for the     General/TarredExecutable could also solve the problem of running an executable transmitted by the client: the binary has to be compatible with the OS on the agent (i.e. 10.2 vs 10.3, as General/XGrid is limited to Mac OS X.2.8+ for now). One could imagine tarring all the versions of the binary and have also a small shell script determine the right version and run it. This script should be aware of its own path, though (I am not sure it is the case for shell scripts; I know Foundation tools are).

Of course, the bigger the     General/TarredExecutable, the more time you waste transmitting it again and again for each task, even if it is to the same agent (future versions will hopefully cache it...).

*
**The working directory.**

If one is provided, the     General/TarredWorkingDirectory is untarred in the second folder created by the agent in /tmp (name is again something like     xgagent.UJ1cV0bY). I believe the     General/TarredWorkingDirectoryName is used by the agent to set the environment variable to this directory before running the command. This will be the 'pwd' directory; any reference to '.' will be this directory.

In addition, you can add other files in this directory by using the     General/TarredFiles. These will be untarred in the working directory and accessible the same way. If you want to pass them as arguments to your command, you should also simply have the filename as one of the arguments - see below.

*
**The arguments.**

The     Arguments are just an     General/NSArray of     General/NSString. Some arguments might coorespond to paths for files in the working directory, including the     General/TarredFiles.



**Jobs and tasks**

So one job consists of several tasks, as many as there are elements in the     General/CommandLineDictionaries array. But I found that you can launch several jobs by repeating the above snippet1 with a different dictionary. Xgrid does not wait for one job to finish before starting a new job. So all the tasks for all the jobs are attributed to different machines. This seems to break the barrier of 251 tasks (see ref5 and discussions in ref1), by using more than one job, each with less than 251 tasks. However, I suspect that the limit might still show up in some cases, for example if several jobs have been started by the controller, with more than 251 tasks total. Also, if you have more than 251 agents (!!!), etc...

The jobs are in the     _jobs instance variable of the controller, which you have access to with the     -jobs method. To cancel all jobs, you have to enumerate that array. Each job will send a separate notification of success (when all of its tasks are done), failure, or cancellation. So by having more jobs, you give more work to the client, which can be a problem if you have too many jobs that are too fast (less than 5-10 minutes). The client will get overwhelmed by the notifications from the controller. However, if you decide to put many tasks in one job, you won't have all the results before the end of all the tasks of the job (you get however intermediate STDOUT and STDERR). I realized these problems after using a non-real-life example (the Dummy plug-in, see part3).


----
*back to tableOfContents*

part2 - 
**Reading job results**
----

The intermediate results that you get from the     jobItemLatestJobResultsDidArrive: notification are described in Apple's template. These give you the latest STDOUT and STDERR in the     [notification userInfo] dictionary. But where do all of the files in the working directory go? The 'Create Custom Plug-in' is able to get them, so why not your plug-in?

The client only gets them when the job finishes, i.e. when ALL ITS TASKS are finished and you receive in your     General/XgViewController subclass the notification     jobItemDidFinish:. The     [notification userInfo] is     nil. However the     [notification object] is a     General/XgJobItem that seems to comply to the     General/XgJob protocol. In particular, it responds to the     jobResults and     finalJobResults messages.

The return values of the messages are both dictionaries with the same 2 keys:

*
    jobIdentifier - a General/NSString that seems to always be a number, reset to 0 for each new session of the client, and incremented for each new job.
*
    General/TaskResultsArray = an     General/NSArray with one element per task affected to the job.


For the     jobResults dictionary, the     General/TaskResultsArray is very similar to the     [notification userInfo] obtained for intermediate results, except that it now contains the whole STDOUT and the whole STDERR of the tasks.

For the     finalJobResults dictionary, the     General/TaskResultsArray is much more interesting. Each element is a dictionary with 0 or 1 of the following key/object pairs:

    
<key>General/TarredWorkingDirectory</key>
<dict>
	<key>General/TarredDirectoryData</key>
	<data></data>
	<key>General/TarredDirectoryName</key>
	<string>directoryName</string>
</dict>

<key>General/WorkingDirectoryFiles</key>
<array>
	<dict>
		<key>General/FileData</key>
		<data></data>
		<key>General/FilePath</key>
		<string>path/in/working/dir</string>
	</dict>
	<dict>
		<key>General/FileData</key>
		<data></data>
		<key>General/FilePath</key>
		<string>another/path</string>
	</dict>

</array>


First of all, you only get data for files that are either new, or modified from the original working directory (I know for sure this is true for the     General/WorkingDirectoryFiles. This reduces transmission of useless data, I guess. The criteria for modification is to have different creation and modification dates. A consequence is that your process have to last more than one minute. Also, if a file has changed but was saved in the first minute of the process, it may not be sent back. Maybe this will be fixed in a future version by also looking at the size of the file. What works for now is to put an older date for modification. I tried

    touch -t 197208171200 filename

and it fooled the General/XGrid agent.

You get EITHER     General/TarredWorkingDirectory OR     General/WorkingDirectoryFiles. You get the     General/TarredWorkingDirectory if you submitted one, and you get     General/WorkingDirectoryFiles if you only submitted     General/TarredFiles. I did not check that you also get the modified     General/TarredFiles in the     General/TarredWorkingDirectory when you submitted both a     General/TarredWorkingDirectory and     General/TarredFiles, but I suspect you should. Also, I don't know what happens if you did not submit     General/TarredWorkingDirectory or     General/TarredFiles but still create a new file. I suspect you get the newly created files back in the     General/WorkingDirectoryFiles.

Now it makes sense why the 'Create costum plug-in' only spits out the files after completing all the (less than 251) tasks. It only starts one job, and only gets all that information from the controller after all tasks are completed.


----
*back to tableOfContents*

part3 - 
**The General/DummyXG example**
----

For testing purpose, I wrote the General/DummyXG plug-in. It can run any number of jobs with any number of tasks per job, each task having a certain duration. The task is a perl script that spends most of its time sleeping (to mimick different durations), and that otherwise takes a very small file and small stdin, modifies that file, create 2 other very small files, and feeds the stdout and stderr. Basically, very small amounts of data get transmitted. You can change their size by modifying the 'afile.txt' and 'stdin.txt' in the plug-in Resources. Have a look at the 'dummy.pl' script too.

This plug-in is really useless and also quite mysterious if you don't read at least the perl script, but most importantly the source code. It can be used as a template for real tasks.

It can also be used to put General/XGrid to work on variable number of jobs and tasks with different duration and data sizes, and to see how the client, the controller and the agents behave, how many jobs succeed and fail in different configurations. I actually found it quite interesting for that purpose too!

You can download the whole thing at
http://membres.lycos.fr/cgparnot/

This is really a temporary place. I don't have any 'real' site to put it.

General/CharlesParnot

----
*back to tableOfContents*


**references:**
----


*
ref0 -
Where it all started...
http://www.apple.com/acg/xgrid/

*
ref1 -
Apple mailing list xgrid-users :
http://lists.apple.com/mailman/listinfo (you need a username and password provided on the site)

*
ref2 - (I could not have done anything without that posting... Thanks very much!!!)
http://lists.apple.com/archives/xgrid-users/2004/Jan/18/howtosendexecutabletoage.001.txt

*
ref3 -
http://lists.apple.com/archives/xgrid-users/2004/Jan/19/howtosendexecutabletoage.001.txt

*
ref4 -
same guy as ref2, with lots of info and a nice tutorial
http://unu.novajo.ca/simple/archives/000022.html
http://unu.novajo.ca/simple/archives/000023.html
http://unu.novajo.ca/simple/archives/000024.html

*
ref5 -
also a nice and clear presentation/tutorial
http://www.macos.utah.edu/Documentation/xgrid/presentation.html

*
ref6 -
I didn't read it yet... well, I should
http://www.beepcore.org/beepcore/home.jsp



----
*back to tableOfContents*

*
Comments on the comments : this is a site for Cocoa development, please keep that as the topic. Other Xgrid problems can be discussed in the mailing list (see ref1). Thanks!!
*

**comments:**
----
