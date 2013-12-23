---
layout: page
title: XcodeGcovTutorial
---



Hi All

Having spent the last week messing around getting gcov to work on Xcode (version 1.5), and having noticed the subsequent hair loss, and that cocoadev doesn't have a gcov tutorial, I thought it would be a good idea to write one, and give a little something back in return for all Cocoadev has given me.

So, first thing to point out is that this tutorial (or maybe it should be called just tips) is written for Xcode 1.5 and not earlier or later versions. Second thing is that there will inevitably be errors on this page. I disclaim responsibility for any lost work, use this information at your own risk.

What is gcov?

gcov is a program that comes with gcc. It instruments your code so that after your program runs, it will write coverage data to a file. This data shows which lines of your program were executed and how often. gcov should be installed on your system if you have gcc.

Why use gcov?

gcov is used during testing to show that your tests are covering every line in your program. In theory, when you know that your tests are covering every line, you can be more confident your program does what it is supposed to. Of course, this is not the whole story and there are many kinds of bugs, to do with memory, threads, etc, that coverage testing doesn't help, but it certainly is a good idea, if you want a better project. You can see exactly what is left to test. gcov can also show you things that might need to be removed if they are useless.

The coverage data can also be fed back into gcc for branch prediction optimizations, meaning that while compiling, gcc knows statistically which branches are likely to be followed more often in your code, and optimize so that the more probable branches are less costly. 

Step by step:

1. gcov requires your executable to be built by gcc with two extra flags, which are -ftest-coverage and -fprofile-arcs.  Let's add these to your build settings. 

*Open up your Project or Target build settings in the Groups and Folders window of Xcode.
*In Styles, scroll down to the Other C Flags and enter "-ftest-coverage -fprofile-arcs". Keep this window open...


2. Optimization of your code may get cause some confusion as to which lines were executed, since several lines may have compiled into one instruction (the same reason debugging is sometimes screwy when optimization is turned on) so we turn it off:

*Scroll down to the Optimization Level and set it to "None [-O0]"


3. (Optional) Clean and rebuild your targets. In theory, this should deposit .bb and .bbg (basic block information) files into your project or build directory, one for each .o file. With gcc3.3.1 it deposited them into the same directory as the .o files.

Now in theory this is enough, but actually this is where I started having lots of trouble. It turns out that gcc3.3.1 has a bug which causes it to mangle the filename of the source code so that when the profiling data is to be written, it gets sent to a illegal path (at least that is what happened to me and to the personwho helped me out). You can run your code fine but the profiling data won't be there, instead you get a bunch of errors. So what we have to do is go back to use gcc3.1. 

4. If you don't recall ever specifically using gcc3.1, or if in doubt, then log onto ADC and download the Xcode 1.5 installer CD. For some unknown reason, if you don't specifically ask for gcc3.1 to be installed, not all the necessary files will be copied during the install and you will get problems relating to a libcrt.o file being missing when you try to compile, even though gcc3.1 is there,. So run the Xcode installer and after selecting the drive to install onto, click the customize button. Select gcc3.1 and install it.

5. Now we need to tell Xcode that we want to use gcc3.1 instead of gcc3.3.1. Open up the Target Info window (double click on the target icon). Click on the "Rules" tab. In the "System C Rule" select GCC System Version (3.1). It should warn you that "the rule is owned by the system, do you want to make a copy?". Make a copy. Do this for the Assembler rule too.

(If you don't mind using gcc3.1 system-wide then it can be easier to just use gcc_select to select which version you want to use. Open a terminal and type "sudo gcc_select 3" (You must be root to change such system settings).)

6. Now there are some things that compile in gcc3.3.1 but not in gcc3.1, specifically, Objective C exception statements (@try @catch and @throw) and the @synchronized() statement. For the former you need to modify your code using the old style NS_DURING NS_HANDLER NS_ENDHANDLER syntax, for the latter you can replace @synchronized() with General/NSLocks. Console yourself that now your code will compile on more gcc platforms and that using General/NSLocks is supposedly faster than @synchronized, (I think I heard about 4 times faster). Your code will still compile on gcc3.3.1 when you go back to it. (If anyone knows any other things that need modifying for gcc3.1 please add a note here)

7. Ok, now we are all set. Perform a clean and build. This should generate a load of .bb and .bbg files in your project directory, one for each source file. 

8. Run your targets. So long as the executable exits normally, it should generate a load of .da files in the same directory.

9. Open up a terminal, cd to your project directory and type "gcov sourcefile.m" where sourcefile is the name of the sourcefile you want profiling for. This will generate a file called sourcefile.m.gcov. Type "less sourcefile.m.gcov" to view the file. You should see your sourcecode with each line of code prefixed with either a number or ###### (or nothing it there was no code to execute there). If there is a number, this is how many times the line was executed. If you see ######, that means the line was never executed, ie, you need a new test to show that it works.

10. It can be a hassle to keep on typing "gcov sourcefile.m", so here is a script you can use to do the whole lot in one go. Create a directory called General/CoverageData (cd General/MyProjectDir; mkdir General/CoverageData) in your project directory and copy the follwing script into a file called coverage.sh (again in your project directory):

    
#!/bin/csh
foreach i ( $* )
 gcov $i ; 
end
mv *.gcov General/CoverageData


call this script with something like "./coverage.sh *.m"

11. To have all the gcov data to hand in Xcode, create a new group in your project called General/CoverageData and Add-To-Project all the files in the General/MyProjectDir/General/CoverageData directory into that group. Now you will be able to flick between them and the source to find out where extra testing is required.

Hope this is useful. Certainly lost me some sleep to figure my way through this.

Happy Coding for 2005!!

-- General/MikeAmy

----
TODO : How to do the branch prediction thing.
----

oooh man, thank you thank you thank you!
